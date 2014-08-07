---
layout: post
tagline: ""
category: pages
tags: [c++, gecko]
---

In Gecko, we recently had a big changeover to using `UniquePtr<T>` instead of `ScopedDeletePtr<T>` and friends.
`UniquePtr` is a stop-gap replacement for `std::unique_ptr`, at least until we have library support for the actual STL version.
I'll be talking about `UniquePtr` here, but it's probably quite similar to how to use `std::unique_ptr`. (probably)

`UniquePtr` usage looks like this:

    struct Foo
    {
    };

    UniquePtr<Foo>
    Alfa()
    {
        UniquePtr<Foo> bar = MakeUnique<Foo>();
        return Move(bar);
    }

    UniquePtr<Foo>
    Bravo()
    {
        auto bar = MakeUnique<Foo>();
        return Move(bar);
    }

    UniquePtr<Foo>
    Charlie()
    {
        return MakeUnique<Foo>();
    }

    // Passing ownership through
    UniquePtr<Foo>
    Delta(UniquePtr<Foo> qux)
    {
        return Move(qux); // Meme: Pass ownership out of the function.
    }

    // Yo dawg
    UniquePtr<Foo>
    Echo(UniquePtr<Foo> qux)
    {
        return Delta(Move(qux));
    }

    // Converting to TemporaryRef
    TemporaryRef<Foo>
    Foxtrot(UniquePtr<Foo> qux)
    {
        RefPtr<Foo> fizz = qux.release();
        return fizz.forget();
    }

    struct Bat {
        UniquePtr<Foo> mBaz;

        Foo* Baz() {
            return mBaz.get();
        }
    }

The weirdest bit about this for me was that we're suddenly passing `UniquePtr`s by value.
When we pass `UniquePtr` by value, we need to use `Move` to pass it into the function.
By calling `Move` on a `UniquePtr`, we pass ownership of the object to the `UniquePtr`.
Let's see an example:

    UniquePtr<Foo> alfa = MakeUnique<Foo>();
    UniquePtr<Foo> bravo;
    bravo = Move(alfa); // Meme: Pass ownership from alfa to bravo.

On the third line here, we call `Move` on `alfa`, so `alfa` gives up ownership of the `Foo`.
UniquePtr<T>::operator= can take a "move reference" (looks like `Foo&&`), which will take over ownership of the move reference.
We pass ownership to functions in the same way:

    UniquePtr<Foo> alfa = MakeUnique<Foo>();
    Delta(Move(alfa)); // Meme: Pass ownership into the function.

What does move do?
---

Let's have an example to talk about:
    UniquePtr<Qux> foo = MakeUnique<Qux>();
    UniquePtr<Qux> bar;
    bar = Move(foo);

In the words of :waldo, it "reaches into the guts of `foo` and sticks it in `bar`".
After moving `foo` to `bar`, `foo` is left in an unspecified but destructable state.
The most trivial such state is just to null out foo, as if we did this:

    bar.reset(foo.release());
    foo.reset(nullptr); // You can also technically just do `foo = nullptr`.

However, `Move` isn't guaranteed to do this.
Move might actually do:

    std::swap(foo.mRawPtr, bar.mRawPtr);

This might just become a register rename, when compiled!
The reason this is still valid is via this reasoning:

* By assigning something to `bar`, we're fine with any previous pointer in `bar` being destroyed.
* `Move` will leave `foo` in an unspecified but destructable state.
* Since `foo` becomes undefined, we should not use its value, nor pass its value on to someone else.
* Since we're not passing on `foo`, `foo` will get destoyed when it falls out of scope.
* Therefore, since we're fine with old `bar` being destroyed, if we just swap `foo` and `bar`, the old `bar` (now in `foo`) will be destroyed when new `foo` falls out of scope.

That is, we can swap them, because we want `foo` in `bar`, but we want to destroy anything that *was* in `bar`, which we can do by putting it in `foo`. (which a well-defined program will allow to be destroyed)

What does this look like in actual code?
---

    [SharedSurface.h]
    class SurfaceFactory
    {
        [...]

    protected:
        virtual UniquePtr<SharedSurface> CreateShared(const gfx::IntSize& size) = 0;

        std::queue< UniquePtr<SharedSurface> > mScraps;

    public:
        UniquePtr<SharedSurface> NewSharedSurface(const gfx::IntSize& size);

        [...]
    };

    [SharedSurface.cpp]
    UniquePtr<SharedSurface>
    SurfaceFactory::NewSharedSurface(const gfx::IntSize& size)
    {
        // Attempt to reuse an old surface.
        while (!mScraps.empty()) {
            UniquePtr<SharedSurface> cur = Move(mScraps.front());
            mScraps.pop();

            if (cur->mSize == size)
                return Move(cur);

            // Let `cur` be destroyed as it falls out of scope, if it wasn't
            // moved.
        }

        return CreateShared(size);
    }

In the abstract class `SurfaceFactory`, we want a way to reuse `SharedSurface`s if they are the correct size and type. (Checking the type is handled in the `Recycle` call)
Therefore, we keep a `queue` of `SharedSurface`s for reuse.
Since this queue owns the `SharedSurface`s, we make it a `queue` of `UniquePtr`s.

If we have any scrap surfaces, we `Move` the `front` to a local UniquePtr, and `pop` the now-empty `front` off the queue.
If the sizes match, we pass ownership out of the function with `return Move(foo)`.
Otherwise, we just let the surface fall out of scope and be destroyed.
If we empty our queue, we just create a new surface with the abstract virtual `CreateShared`.

Using `UniquePtr` here allows (and forces!) us to be very explicit about ownership of objects.
This reduces the need for comments regarding ownership, and just plain makes things more clear.

You can see how we were working with bare pointers before in [SharedSurface.h](http://hg.mozilla.org/mozilla-central/file/855997734816/gfx/gl/SharedSurface.h#l114) and [SharedSurface.cpp](http://hg.mozilla.org/mozilla-central/file/855997734816/gfx/gl/SharedSurface.cpp#l265).
There weren't even comments regarding how `mScraps` owns its raw pointers. (My bad!)

More
---

Thanks to :waldo for helping me understand UniquePtr.
He has a blog post that goes into more detail over at <http://whereswalden.com/2014/07/31/mfbt-now-has-uniqueptr-and-makeunique-for-managing-singly-owned-resources/>.

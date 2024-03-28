# Spin Lock

Spin lock is one of the locking mechanisms used in the kernel for managing resources like how semaphore is being used.

SpinLock uses a busy-waiting mechanism in which when it tries to access a resource, it won't go to sleep/suspend, it is still on a running/active stage and starts an endless loop asking whether the lock is released or not.

while(lockNotReleasedYet) {
// ask again
}

It works if the resource sharing is at short-held lock, since semaphore has a list (wait-queue) of to keep track of the lock and it goes to sleep state and then waking up again but here on spin lock it is still on running state, it's more faster for a shorter locks.

For longer-held, other synchronization techniques make more sense because we'll be wasting cpu cycles if we used spin-lock.

It is used in more lower-level driver access systems.

kernel docs have different variations of this 🍿

https://docs.kernel.org/locking/spinlocks.html

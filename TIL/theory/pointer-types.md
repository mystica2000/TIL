
### Dangling pointers 
- arises when the memory may be free while there are reference to it.

For example:

A -> B -> C is a object

If B gets deleted, 
then A - ??? - C

This results A with dangling pointer since A now points to deallocated B's address coz B doesn't exists and there's no way to reach to C (it becomes unreachable consuming unwanted memory)

### Fat pointers 
- stores meta data along with address. It can be used detect dangling pointer for memory safety.

obj = A (ver, (ref, version)) -> B (ver, (ref, version)) -> C (ver, (ref, version))

If obj is accessed in concurrent environment where reference A gets changed on one method then on the other method it is accessing the reference, it checks if version number stored in pointer matches with the object, if yes proceed. If not, then might be dangling pointer. 

However solely checking version number alone won't be accurate since what if the memory gets deallocated and allocated with the same version number.

Fat pointers have more use cases than just detecting dangling pointer and contains more meta data than just version number. 

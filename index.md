# Dynamic Array Optimization

As a student at the SAE Institute of Geneva in the game programming section, I was tasked to implement and to optimize some of the basic features of a game engine. 

Here, I'll be presenting my work on DynArrays.

![](https://github.com/GJeannin0/Gjeannin0.github.io/blob/master/Images/array.jpg)

One problem with fixed-size arrays is that the programmer must assume a size for the array. This may lead to erroneous assumptions that can be the source of a lot of bugs. Because the maximum can be exceeded, directly causing bugs, and the array can be way bigger than needed, wasting memory.

Another problem is that the array is not expandable. Using a small size may be more efficient for the typical data, but prevents the program from running with larger data sets.

The standard library includes vectors that can avoid these problems, and the DynArray is a simpler vector.

Both of these problems can be avoided by reallocating an array when it needs to expand. This is exactly what my DynArray does, let's see how it operates. 

## What's in the class?

It is declared as a pointer and has a FreelistAllocator that allocates memory to it on instantiation and when it needs to expand.
The template allows for the DynArray to be used with any type of element.
And it keeps its size and capacity in memory.

```cpp
template<typename T>
	class DynArray	{
	private:
		FreeListAllocator& allocator_;
		size_t capacity_ = 0;
		size_t size_ = 0;
		T* data_ = nullptr;
```
The [] operator gives access to the elements the DynArray contains.

```cpp
T& operator[](size_t index) {
	neko_assert(index >= 0 && index < size_, "[Error] Out of scope access");
	return data_[index];
}
```

It also includes simple methods to get its size, to get its capacity, to insert an element and to push an element.
Which can be needed to work with DynArrays.

```cpp
size_t GetSize() const {return size_;}

size_t GetCapacity() const {return capacity_;}
```

## Memory allocations

I use a custom FreelistAllocator because it connects unallocated regions of memory and it is perfect for dynamically allocating memory.
When the first element is pushed in a DynArray, it allocates the amount of memory needed for two elements and ensures that it is correctly aligned.

```cpp
if (data_ == nullptr) {
	capacity_ = 2;
	data_ = (T*)allocator_.Allocate(sizeof(T) * capacity_, alignof(T));
	data_[0] = elem;
	size_++;
}
```

When an element is pushed in a DynArray that is already full of elements, the allocator allocates double the amount of memory to the DynArray, thus doubling its capacity. It also deallocates the memory space that is no longer used by the DynArray.

```cpp
if (size_ + 1 > capacity_) {
	capacity_ *= 2;
	T* data = (T*)allocator_.Allocate(sizeof(T) * capacity_, alignof(T));
	for(int i=0; i<size_;i++){
		data[i] = data_[i];
	}
	data[size_] = elem;
	size_++;
	allocator_.Deallocate(data_);					
	data_ = data;
}
```
Multiplying the capacity every time it needs to expands allows the DynArray to adapt its capacity with few allocations in order to perform well even if a lot of elements are being pushed in, while its capacity won't use too much memory for the size it needs to contain.

The amount to multiply the capcity with is determined by the memory budget available and the cost of syscalls.
Because we have several Gigabytes of RAM available, doubling the capacity is a good ground.

## Perfomances

![](https://github.com/GJeannin0/Gjeannin0.github.io/blob/master/Images/bench.png)

Compiled on Windows 10, CPU Intel(R) Core(™) i7-9750H 2.60 GHz

DynArray is faster than std vector for pushing data, and that's good.

## What I learned while working on DynArrays

I had to learn about memory allocations to understand the different ways it can be done and how much it costs to use. 
For example I learned how having aligned memory could help a program operates faster, just by using the right addresses for the right elements.

I also learned to use tools I never used before like allocators.

Despite it being complex to learn, this knowledge helps me better understand how low-level optimizations can help game engines perform better.

## Conclusion

It was a good experience working on DynArrays, as it is a low-level task it required me to do more research than usual to understand the matter, and that allowed me to become more sensitive to how I implement things.



Thanks for reading me.

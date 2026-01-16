# Arraylist vs Linkedlist

## 1. Array

​	Arrays contain elements each occupying a position. In programming, the first position is usually 0, the second is 1, and so forth. These positions are called indexes which we use to retrive and change the element at that position in the array. At low-level, the idea is to maintain each array element adjacent to one another in the memory. 

​	Since memory also holds other values, array should not inherently expand. Disregarding the fact and adding elements could potentially overwritten other values that might be required later. This explains why arrays and systems programming languages lack methods such as append, insert or push as seen in Python, Javascript or other scripting languages, apart from the fact that arrays aren't objects at low level.

### Arrayl in system languages 

​	When the array is declared, we specify both the type of elements it will contain, and its length, indicating how many of those specific elements the array will contain. The inability to have different types within the same array, lies in <u>the use of this information to determine the required memory space for the array</u>. The type specification informs the compiler about the number of bits each element occupies in the memory. By multiplying this value by the array's length, the precise amount of memory required to store the entire array, can be determined. Whether the array is stored in the stack or the heap, this information is quite essential to know how much to move the stack pointer, or to allocate a sufficiently large memory space respectively. 

​	In bare hardware, the only information available to us, is the memory address where the array begins, referred to as the base address. Since we know the number of bytes each element occupies within the array, when we want to access a specific element, we can calculate the memory address where that element starts. To achieve this, we add the product of the element's size and its index to the memory base address. ~~This explains why we utilize 0 instead of 1 for the first element.~~ ~~Access the first element, moving the pointer is unnecessary.~~ 

​	**Array itself does not contain information about its size**. Array is simply *a block of memory lacking an inherent property*, such as length to verify the validity of an index being passed. 

​	There is high likehood of attempting to access an element outside the actual bounds of the array. There are two possible scenarios:

1. If the array is allocated at or near the end of the memory granted to our process, the OS will detect attempts to access memory beyond our allocated space. In this situation, the OS terminates the execution of our process, resulting in a segmentation fault error. 
2. If the array is allocated futher away from the memory limit the OS will not terminate the execution, since we'll be accessing memory within our process's boundaries. ~~But be careful with this situation.~~ 



## 2. Arraylist

​	Instead of just holding a reference to an array, we create a **data type that includes that reference plus additional information** (capacity, length) about the array. When created, this data type allocates memory for an empty array. The capacity attribute indicates how many elements the array can hold. The length attribute indicates how many elements the array contains at any given time. 

​	The idea behind this data type is that it'll handle the array for us with some associate methods. 

​	The push method accepts a value and adds it to the array. Every time, we add a element to the array,  the length attribute increases. When array is filled with elements, to prevent adding values beyond the array's capacity, we need to check whether the array is full when adding an element (whether length equals to capacity). If the array is not full, we can safely add the value of the new element.  However, if the array is full, we need to allocate a larger array to accommodate more elements. Typically, we allocate a new array **<u>twice</u>** as long as the previous one. During this process, we update both the capacity and reference to the new array. This involves copying all the values from the previous array to the new one. Once the elements are copied to the new array, the previous array is no longer needed, so we free its memory. Finally with free sapce in the new array, we can add the new element and update the length accordingly. 

​	Another characteristic that arraylists inherit from arrays is **constant time access**. Since elements are internally stored in the array, accessing an element at a specific position simply involves retrieving the element from the array by indexing it. 

​	To prevent accessing elements beyond the array boundaries, we perform a validation before indexing the array. If the provided index is greater than or equal to the array length, we can return NULL, None or a proper error. 



## 3. Linkedlist

​	The simplest method to obtain a dynamical sized list of elements is by storing them wherever there is available space. Create a custom data type called a Node. A Node contains one item and one pointer. The item represents the element of the list and the pointer directs to the address where the next Node, containing the next element resides in memory. We only need to maintain a reference to the first Node, typically in the stack. 

​	When we wish to add a new element to the list, we search for an available space big enough for that Node, then we update the last Node of the list point to the new Node, which becomes the last one. This approach eliminates the concern about the list of elements growing. 

​	However, linkedlists suffer from a significant drawback    --    <u>they are not efficient for caching data</u>. 

​	When the data required for a CPU operation is found, we refer it as a **cache hit**. Conversely, if the data is not in cache, we refer it as a **cache miss**. 

​	Due to cache's limited size, the cache cannot store a copy of the entire memory. For optimal performance, data should ideally be as compact as possible, allowing it to fit into the cache, and increasing the chance of cache hit. 

​	As linkedlist's elements are scattered throughout memory, they are very unlikely to be cached. Arrays keep elements compacted in memory, which make them better at caching.



## 4. Arraylist vs Linkedlist

​	Unlike the arraylist, linkedlist don't provide direct access to elements by index. As elements are scattered in memory, there is no formula to determine the address where the elements resides. Accessing an element as a specific positoin in the linkedlist, requires traversing the linkedlist from the head until reaching the node in the desired position. Since each node must be traversed individually, the time complexity for indexing in quotes, <u>a linkedlist is linear</u>. 

​	Even if the desired element is in the cache, the other nodes along the path to find that element are often not cached, which can significantly slow down the traversing process. 

​	When it's up to remove operations, after deleting an element in the array, all the subsequent elements must be shifted back one positoin in the array. Copying data in memory can be time consuming. If the removed element is closer to the end of the array, the shifting process is faster, since there are fewer elements to shift. If the removed element is closer to the beginning of the array, there are more elements to be shifted, which can significantly slow down the process. Remove operations for linkedlist is faster, because everything we must do is to remove an element is to update its previous node to point to the following node, and then free the unwanted node's memory. As no shift operations are needed, the time complexity of this operation is constant. We should also consider that, in order to update the desired node, we first need to find its value in memory. 



## 5. Generic type

​	When declaring a generic type, the compiler identifies each instance where we use that generic, generates source code specific to that type and substitutes the generic type with the generated type. 



## 6. Custom type

​	If the type contained by the array is not primitive, instead of creating an array of values, the VM is actually creating an array of pointers pointing to the memory address where the memory resides in memory. This is quite bad for caching. Linkedlist can get even worse, because nodes won't contain actual element and the pointer to the next node, but a pointer to the element and a pointer to the next node. A cache nightmare. 



## 7. How do script languages handle arrays ?

​	The scrip languages are typically not compiled into machine code, instead, they rely on a special program to interpret the script, allowing it to emulate the desired behavior. To execute that emulation process, the real hardware is used. For this reason, the programs are often referred to as interpreters or virtual machines. When we declare an array, behind the scenes, the interpreter generates a specialized data structure to simulate an array elements. 

### Python

​	PyListObject is what interpreter generates when we create a Python list. PyListObject type is nothing, but an array of object pointers. So, Python does, is similar to Java does for primitives types  --  An array of memory addresses pointing to the values. There is a key difference, in Java, all pointers within the array pointer to values of the same type, in Python these pointers point to objects, implying they can point to values of different types. 

### Javascript

​	Javascript arrays aren't actually arrays, they are hashmaps. Javascript doesn't care itself much with arrays in memory. Instead, when a Javascript array is declared, the engine creates a hashmap where key is correspond to the indexes. Indexing process is essentially syntax sugar for retrieving an element from the hashmap by its key.
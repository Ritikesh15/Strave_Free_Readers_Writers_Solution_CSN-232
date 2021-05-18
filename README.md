# Strave_Free_Readers_Writers_Solution_CSN-232
The Readers-Writers problem is one of the most basic and common problems in process synchronization. There are two kinds of users the Readers, who want to read the given resource, and the Writers, who want to revise or modify the shared resources. There are three distinctions in this problem.

### First_Readers_Writers_Problem
This problem asserts that while at least one reader is reading the resource, new readers can also enter the database and read that resource. However, in this case, the writers may begin to starvation as they may not get any chance to modify the resources as new readers might continuously access the resource.

### Second_Readers_Writers_Problem
This problem is also similar to the above problem. However, in contrast here, the readers may starve. Preference is given to the writers in this problem. The readers must wait until the last writer exits the database and clear the barrier to allow readers to access the resource.

### Third_Readers_Writers_Problem
This problem subdues the shortcomings of the above two starvation problems and hence is also known as Starve_Free_Readers_Writers_Problem. It will give preference to the user in the order of their arrival time. For example, if a writer wants to write to the resource, it will wait until the current readers complete their reading. Meanwhile, other readers trying to access the resources would be halted to access the database.

### Psuedo code of Starve_Free_Readers_Writers_Problem
For third readers-writers problem,
``` 
unsigned int readCount = 0; // number of readers reading at current moment
semaphore order; // to preserve the order of arrival of readers and writers
semaphore getAccess; // to decide either writer or reader has access to the critical section
semaphore readMutex; // to make sure that readers count can be changed only by one reader at a time

void read(){

    wait(order); // preserves the order of arrival of readers and writers
    wait(readMutex); // gets the mutex to change readers count
    
    if(readCount == 0){
        // If the reader is the first reader, get the access mutex to avoid entry to writer while current readers are reading
        wait(getAccess);
    }
    
    readCount++; // incrementing the readers count
    signal(order); // releases the order of semaphore as tasks are completed
    signal(readMutex); // releases the reader semaphore for allowing entry to other readers
    readResource(); // reading the resource
    wait(readMutex); // getting the mutex for changing of the readers count
    readCount--;
    
    if(readCount==0){
        // If the reader is the last reader, release the getAccess semaphore allowing the writers to gain access
        signal(getAccess);
    }
    
    signal(readMutex); // releasing the mutex as the count is updated
}

void write(){

    wait(order); // preserving the order of arrival of writers and readers
    wait(getAccess); // gets the access mutex for denying entry to readers while writing
    signal(order); // releases order semaphore as as tasks are completed
    writeResource(); // writing of resource by writer
    signal(getAccess); // releasing the access to the resource allowing other users to get entry
}
```

### Explanation of the Psuedo Code
We will consider use of the following three semaphores:
1. **readMutex:** It would be used while updating the readers count. Therefore it would only be available to readers method.
2. **getAccess:** This would be either in control of readers or the writer. If the readers are reading, then if the writer would try to access the database, it would get blocked and vice versa. However, if one reader is reading and another reader tries to access it, there will not be any problem. This semaphore gets updated in three instances. First, when the first reader arrives. Secondly, when the last reader left the database. Furthermore, last, when any writer writes to the resource.
3. **order:** This semaphore is used at the start of the entry section of readers/writers code. This only checks that if there is any process already waiting for its turn. If so, it gets blocked. If not, it accesses the semaphore, and no new reader/writer could now execute before this process. Therefore, it helps in preserving the order of process.

We are using a variable readCount to update the number of readers at a particular time.

* For the read method, first, we would call wait for order and readMutex. If a process is in the queue, then the order will be 1, and hence, the calling of the process will be blocked. Otherwise, it will make order "1". readMutex will check that there is no other process that is updating the readers count. If the readCount was 0, then it will not allow the writer to access the database. After the readCount is updated, both the semaphores are released. After the reading of the resource, readCount is decremented by getting hold of readMutex. If the readCount is 0, writers will then be able to access the database.
* For the write method, we will first check the order semaphore, and then access will be given to the writer with the getAccess semaphore. The order will be preserved, and thus we could release the order semaphore. Then, writers will modify the resources and then finally release getAccess.

Thus, by this algorithm, we have successfully solved the problem.

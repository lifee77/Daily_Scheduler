# Daily_Scheduler
It takes A series of Tasks as Input and gives an efficient schedule for the day. 

**Introduction:**

A scheduler is an application that takes several tasks with different attributes like starting time, duration, etc, and executes those tasks at specified periods. In the implementation for this project, we have tasks without specific starting times so the program uses priority values based on a utility calculation. We used Python Classes and several in-built functions and libraries to complete the implementation.

**Tasks:**

| Task ID | Description | Duration(mins) | Flexibility | Start time | Dependencies |
| --- | --- | --- | --- | --- | --- |
| 0 | Get up at 9 AM | 10 | False | 9 AM | [] |
| 1 | Eat Breakfast | 40 | True | - | [0] |
| 2 | CS110 Prework | 240 | True | - | [0,1] |
| 3 | Cook | 120 | True | - | [0,1] |
| 4 | Eat Lunch | 30 | True | - | [0,1,3] |
| 5 | Hike to Namsan | 60 | False | 7 PM | [6,4] |
| 6 | CS110 Class | 90 | False | 4 PM | [2,4] |
| 7 | Cafe Hopping | 120 | True | - | [3] |
| 8 | Preparing Kimchi | 30 | True | - | [4,5,6,7] |
| 9 | Sleep | 5 | True | - | [3,4,5,6,7] |

**Table 1**. List of Location-Based and regular activities I complete on typical Mondays. Tasks in bold are city-based experiences.

Most of the tasks like having breakfast, waking up, and sleeping, are significant tasks that feel attached to our day. I often go hiking to different parts of Seoul. I have used Namsan as a representative of those tasks as it’s a popular running spot among Minervans [context: Minerva 5k running event organized by Prof. Hadavand]. I like cooking. It adds utility to my mental and financial health as I get to eat food from home and several other cuisines for cheap. I explore cooking vegetarian Korean dishes and I have used kimchi as an example. Seoul is popular for its cafes and the working environment there. The cafes are open till early morning and are very comfortable and safe to work. So, this is part of my daily routine too.

Each task has several attributes, which are all stored with the task and help in calculating the priority value. The attributes are described below;

- Task ID: a unique identifier for the task.
- Description: a string describing the task.
- Duration: the duration of the task in minutes.
- Dependencies: a list of task IDs that this task depends on.
- Flexibility: a boolean indicating whether the task is flexible.
- Start time: the start time of the task (in minutes past midnight). For flexible tasks, we **don’t need start times.**

### **Utility Calculation:**

****Minerva is an online residential program. Most of our classes, meetings, and programs are held online. So, it is necessary to have a scheduler to maintain the loads of information about events. Some of the tasks we perform like the Pre-work, going on a walk, etc are time-flexible. Meaning that doing those tasks at a different time does not hamper us. So, we tend to show risk-seeking (if good at that subject) or risk-neutral behavior in such events.

However, some tasks like the CS110 class or an internship interview are non-flexible. We will get huge penalties for missing classes like course withdrawal or missing an internship opportunity by missing interviews. Hence, non-flexible tasks get huge weight in utility calculation. Similarly, many tasks will be dependent on one task. Eg, having lunch will have an impact on other tasks; I physically cannot attend a 4 PM class without having lunch. Hence, tasks with many dependents will get priority.

U = flexibility* [10 if (currenttime -starttime) = 0 else: 0] + 0.3*no ofdependents

Here,  Flexibility = [1 if True else 0]

Here, flexibility has a weight of 10 as I want this to exceed all other priorities ( **be bulletproof**) despite the number of dependents and be performed as we are risk-averse for non-flexible behavior. Tasks with higher dependents are given a weight of 0.3 so that they can never exceed non-flexible tasks but still have a very high impact on the utility when the task is flexible.

Only two significant attributes are used to calculate utility for simplicity. To increase the efficiency of the scheduler, more factors like splittable tasks (if a task can be split into two) could have been split into two or more sub-parts to fill in empty slots in the day and given very high priority when we have such empty time slots (especially created by the non-flexible tasks’ timing).

**Algorithmic Strategy:**

**Why priority queue?**

For this problem, we need to organize data based on priority values calculated using the utility function. We had the option of using Python arrays (lists), the heap data structure (min or max), or other data structures. I used a maxheap for the priority queue to solve the problem because;

1. **Efficiency in Adding/ Deleting Elements:**

Since we are making a daily scheduler, it’s common to add new tasks or delete existing ones. If we use lists, we will have to resort the list again which even for the most efficient sorting algorithm like quicksort will take O(nlogn) time complexity scaling. For a sorted list, we will need a time complexity of O(n) to insert an element as we only have to compare with n elements. While the time complexity to add or remove elements from a priority queue (max heap) is only O(logn). This is because when adding an element in a max heap, it might violate the heap property and thus to maintain the property we need to swap this element down the tree, which at maximum will take O(logn) time.

1. **Accessing Max Element:**

****Accessing the maximum element in a list will take O(nlogn) time as we need to sort but it will only take O(1) time if the list is sorted. However, we need to know the length of the list if sorted in ascending order. While priority queues (Max Heap) will always give the maximum priority element in O(1) time.

**Using one vs two priority Queues:**

While calculating the priority queues, we have a problem incorporating the non-flexible tasks in our priority value and performing them at the exact time as missing them has severe consequences. I had two choices to choose from.

1. **Using a Max Heap and Min Heap:**

**Setting Up:**

This was the most straightforward method. If I were to implement this method, I would

check if a task is flexible or not. If the task is non-flexible I would add that to the min heap with the starting time as the key. Here, the priority value does not matter as they should be completed in the starting time specified. The flexible tasks would go in the max heap with the priority value as the key. These operations would take O(nlogn) time complexity while forming the heaps.

**Working:**

While implementing this approach, I would first check the Min heap (priority queue) if the current time is equal to the min value of the priority queue or if the time duration of the max element of max heap + current time is greater than the starting time of the non-flexible task. In this case, we either wait until the starting time or execute a splittable task from the Max heap. Hence, the non-flexible tasks are guaranteed to run on time.

**Limitations:**

Despite being straightforward in logic, this involves more hassle in implementation and understanding the code. This is because of the fact that we are dealing with two heaps and have to maintain both of them. Also, we need ~200 lines of extra code (for the min heap class). Maintaining two heaps also takes more memory than using a single heap.

1. **Using a Max-heap and embedding flexibility in priority value:**

**Setting Up and Working:**

As my [Utility Calculation](https://docs.google.com/document/d/1eGM9wdwd-q3mSFUM2Or-f3A9OrzlW19JUwc0YbGnHS8/edit#heading=h.gxm37gp0vnl9) specifies, we can store all the tasks in one priority queue (Max heap). We will use the priority value as the key for the heap. This method involves checking if the current time is equal to the starting time of non-flexible events and giving them a high priority. Also, if the duration of the maximum of either child node (one of which will be the 2nd highest element) + current_time is less than the starting time of the max_element (the non-flexible event), they still get the high priority.  Here, we either execute a splittable task (half of a task in lower priority) or wait (take an eye rest) until the non-flexible task starts. If we execute a splittable task, we will only run this for the duration:

*starting_time  of non-flexible task -current time*

We then update its time duration attribute but **do not heap pop** the task. We then execute the non-flexible task. Hence, this method also ensures that the non-flexible tasks are executed exactly at their starting point.

Using one priority queue method simplifies the scheduling process. We only need to manage one queue, and we can always select the next task to execute by simply popping the highest-priority task from the queue or a child in case of a splittable task. This approach also ensures that all tasks are considered together when determining the next task to execute, which could lead to a more optimal schedule. This method also saves memory as we will only work with and store one heap. Building and maintaining a heap will take O(nlogn) time complexity similar to the previous method.

**Limitations of my Implementation of this method:**

While this method seems to work perfectly, my implementation doesn’t work exactly how this describes. My implementation is missing this component:

**It only tests if the current time is equal to the task start time**. Since we are working with minutes as units of time, it will be almost impossible to find exact values. Hence, this will prioritize a task starting at 3:55 even if I have a CS110 class (non-flexible) at 4 PM.  I tried using the approach I described but had some problems accessing the left and right child nodes.

Accessing the child nodes would be the best way to solve this issue but we can also **set some constraints in the input** and change the code slightly to get the desired results. We can set a constraint that each task should be 1 hour long. This way, we will always come and test at each hour point and then the non-flexible tasks will have the maximum priority, and hence they will be executed. However, this might not be practical.

**Working of the Scheduler:**

![image](https://github.com/lifee77/Daily_Scheduler/assets/83622253/6f23b2fd-9e3e-461a-9e6a-ceda8d720aab)

**Figure 1.** A flowchart of the daily scheduler. It shows the high-level operations to show the flow but not all the functions involved in the solution.

The Scheduler has a tool like the conductor of an orchestra named run_tasks. It makes everything happen in the right order and at the right time. The orchestra runs the show as follows;

1. **Setup:** The Orchestra starts by setting its watch to the starting time you give it (the time you start your day). It also creates a box to store the tasks you complete at every point of your day. You can use this box to reflect on how you spent your time this day.

2.  The orchestra has a list of tasks that you promised to complete today. It will go after one task each time unless all the tasks are completed.

3. **Preparing to Do tasks:** For each task, the orchestra calls their beautician assistant. This assistant goes through all the tasks and checks if they are ready to be executed in your day. You cannot repeat the same thing and you cannot change the natural order of things. For example, you cannot eat your lunch before you wake up from bed. This is pretty intuitive, right? After the beautician assistant prepares the tasks, it adds them to a to-do list that always keeps the most important task at the top so you don’t forget anything.

4. **Executing Tasks**: If there are any tasks in the To-do list, the orchestra uses its magic to know what’s important to you. The secret magic ingredients are;

- How many of your tasks are dependent on each task
- Do you have to do something at an exact time or the time does not matter?

Based on this, the To-do list is maintained. The orchestra then picks the most important task (the one at the top of the to-do list) and sends it your way to complete it. This is like crossing a task off your to-do list. The orchestra then adds the task to the bucket that it made initially and updates the current time by adding the time duration needed to complete the current task.

5. **Updating Dependencies:** After a task is completed, the orchestra removes this task as a requirement for other tasks. Remember, we talked about not having lunch before waking up. Now, since the orchestra marked that you are awake, you can happily eat lunch :).  We can also think of this as telling your friends that you've finished your part of a group project, so they can now do their parts.

6. **Repeat until done**: The orchestra goes back to another task and repeats everything. When all the tasks you promised to complete today are done, it will ask you to go to sleep (before 4 AM). It will also give you the time duration it took you to complete all tasks and the bucket that had the set of tasks you completed today.

Happy Reflection on your day:)

Note: Failures/Limitations of the implementation are described when describing the algorithmic approach.

**Complexity Analysis:**

**Theoretical:**

****The flow of the program in my implementation starts from the run task scheduler function. So, we will start our complexity analysis from there.

![image](https://github.com/lifee77/Daily_Scheduler/assets/83622253/76ab0e9e-5987-4d9b-b98a-8cd038599104)


The run_task_scheduler function has a while loop. It iterates through all the elements of the heap. So, this loop has a time complexity of O(n). The while loop calls a function check_unscheduled tasks. We will take a look into this function.

![image](https://github.com/lifee77/Daily_Scheduler/assets/83622253/de08983b-73c2-41f0-9828-7f6e4b9b3132)

Since this function will run every time the while loop runs and it has a for loop inside which runs for O(N) time, the scaling for the algorithm so far is O(N^2).

A line after the while loop is defined inside the run_task_scheduler function, get_tasks_ready (another function) is called.

![image](https://github.com/lifee77/Daily_Scheduler/assets/83622253/8665140e-b509-460a-bdfb-e1543a8f1ebb)


This function has two ‘for loops’. But, none of these are nested inside the for loop from the check_unscheduled_tasks function. These two aren’t nested with each other either. Hence, they are still the 2nd order nested loops for the primary run_task_scheduler function. This gives a time complexity of O(N). Then it pushes the tasks back to the heap which takes a time complexity of O(logN). Hence, it has a total time complexity of O(N log N)

Hence,  the scaling of the overall algorithm so far will be O(N^2* logN).

But, there are more operations to perform that do not multiply to the runtime of the overall algorithm because either they are inside the same for loop so act as constant, or have a constant runtime;

- We use heappop several times to eliminate the element with the highest priority. This has a runtime of O(1) as it always has to access the first element of a max-heap and does not scale with input size.
- The cost to initialize the attributes of different classes is O(1) as we only initialize them once. Hence, they do not scale with time.
- Print_self method: This method has a time complexity of O(N) because it iterates over each task to print its details. But, this isn’t inside the nested loop and is only printed once. So, it doesn’t count towards the scaling (lower power).
- Remove_dependency method: This method has a time complexity of O(N) because it iterates over each task to remove a dependency. Count_dependents method: This method has a time complexity of O(N) because it iterates over each task to count dependents.
- Priority_value method: This method has a time complexity of O(N), where n is the number of tasks. This is because it calls the `count_dependents` method which has a time complexity of O(N).
- Format_time method: This method has a time complexity of O(1), as it simply formats the given time and does not scale with input size.

Hence, the overall time complexity scaling to run the Scheduler is O(N^2*logN) with my approach.

**Experimental Analysis:**

![image](https://github.com/lifee77/Daily_Scheduler/assets/83622253/90faa5d5-89ee-411e-985d-7ac1430aae2f)


**Figure 2.** *A runtime complexity scaling plot for the Task Scheduler program. The runtime is measured in seconds. The number of tasks are task dictionaries given in each input.*

Theoretically, I got the scaling to be O(N^2 *logN). Considering that we are using heaps, this is not the best scaling. So, I tried experimental analysis by running the using tasks from Input size 1 to 100, and 1 to 1000. I used the random library to assist with this process.

![image](https://github.com/lifee77/Daily_Scheduler/assets/83622253/800d051f-fd72-4c61-8a02-4b14e87665f3)


**Figure 3.** A runtime complexity scaling plot for the Task Scheduler program. The runtime is measured in seconds. The number of tasks are task dictionaries given in each input. The red color indicates the line of best fit and the scatterplot with the blue line is the experimental runtime complexity.

In Figure 2, there are many fluctuations to conclude if it’s a linear or a quadratic graph. Hence, I made a line of best fit. The line of best fit and the runtime graph look very similar. However, there are some points that go outside of the line and very far off. This might be explained by other processes running in the computer at that time as such points are not significantly high.

The surprising analysis here is that, theoretically the runtime scaling was supposed to be O(N^2*logN). However, it looks similar to O(N) now. There is one reason to explain that discrepancy; we were accounting for the worst case when doing the theoretical analysis. However, many tasks did not even have dependencies, so it didn’t have to loop over all the N elements. Similarly, only about half of the tasks were time-inflexible.

Hence, we can infer from this case that O(N^2logN) was a loose or far bound. We cannot use it as a big theta (N^2*logN).

Video Explanation:

https://www.loom.com/share/e4df5ac4dde14db587c724ed6b1e85b5?sid=fdccf38d-afdc-4ae1-8602-1d5f9619d7ba

**References**

Bhatta, J. (2019, March 9). *Time Complexity Graph Code*. Minerva Forum. Retrieved October 30, 2023, from https://sle-collaboration.minervaproject.com/?id=aafb696a-b764-4952-b09e-8d25626f40ad&userId=12372&name=Jeevan+Bhatta&avatar=https%3A//s3.us-east-1.amazonaws.com/picasso.fixtures/JEEVAN_BHATTA_12372_2022-05-10T06%3A10%3A55.343Z&readOnly=0&isInstructor=0&si

Minerva University. (2019, March 9). *CS110 Session 13*. Minerva Forum. Retrieved October 29, 2023, from https://forum.minerva.edu/app/courses/2894/sections/11057/classes/78017/review?tab=workbooks

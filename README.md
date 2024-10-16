Problem Description:
Write a program that reads a text file that contains 0's and 1's in a regular N x M sized grid. Here is an example of what a valid input file looks like:

0110
0100
0010
0011

After reading the file, your goal is to find the number of "connected shapes" in the data. A connected shape is defined as a group of 1's that are immediately adjacent to each other. The 1's must be immediately to the left, right, top, or bottom of each other to count as connected (diagonals do not count as connected).

In the example above, your program would return 2, because there are two connected shapes:
11
1
at position (0, 1) and
1
11
at position (2, 2)

You only need to return the number of connected shapes that you've found. You do not need to return the position of the shapes.

The above prompt is exempt from the license below and was provided by Maestro for the purpose of evaluation for employment.
Anything below this is the work of and property of David Gabriel
Notes
A brief video explaining the solution. https://drive.google.com/file/d/1rq0U3h8kZYhOEYDgCEWrZup8PpEBbg9U/view

Key Assumptions
At first, as always I wanted to explicitly state the assumptions and define any tricky edge cases before coding. The following assumptions were made in the completion of this task.

Single 1's are a "group" or "shape".
Only "valid" inputs allowed. That means for instance, we will ignore possible cases where, maybe a list, or an escape character is inserted where a single 0 or 1 is expected. Also, only 0's and 1's in rectangular grid.
Goals
The goal is to solve the problem as stated, but also to do so with consideration towards:
-Accuracy in all cases
-Minimum algorithmic complexity
-Possibility to parallelize the algorithm and distribute parts of the workload

Mapping Stage:
This approach is to scan all the lines one at a time, detecting 1's. When a 1 is detected, We check for adjectent known shapes that it would be connected to according to the prompt. If we see that an adjacent shape is detected, we add this 1 to that shape. Shapes are tracked in a hash map as keys. The idea there is to potentially reduce the complexity to check if a 1 is a shape member (O(1) instead of O(N) where N is the number of shape members). This was not fully implemented in this version due to time constraints.

Now this is all well and good, but there's a catch. When we scan into a shape, it is possible we see a shape member, but have not yet scanned its neighboring shape members. In this case, we detect this as a new shape. The problem is we end up creating two adjacent shapes that actually should be connected. The mapping phase has complexity O(MxN).

Reduce Stage:
In order to solve the problem of detecting a new shape, because its adjecent members are unknown, we apply a reduction. We iterate over the shapes we have detected, and look for connected shapes in order to join them. This join process has complexity O(4P^2) where P is the number of shape members in the space. The four is because it checks in 4 dimensions. In different dimensionality spaces this would change accordingly.

Parallelizability
The mapping stage is embarassingly parallel, if using something like a dataframe to store indices. If sharding the space, we may need an iterative reducer, depending on the sharding type. This could conceivably be a very scalable solution, especially for sparse spaces.

Boundary Walk (another possible solution)
So, I began with the concept that I would instantiate a 2d cursor and use iterators to search for the first 1 in the matrix. Pretty standard idea, but not the only approach. Then what we do next once the shape is first detected is the most important part of this algorithm design. I investigated some concepts related to boundary traversal. That is walking along the perimeter of the shape and determining an outer edge of the shape. I thought this could possibly reduce number of array accesses. However, in praticality such a walking algorithm is complex. Something like a Tremeau walk, but modified for boundary traversal might work. Unfortunately for many shapes present in the small data set, this actually tends to increase the the number of access, to determine the correct path around the shape. Additionally, this algorithm stage starts to accumulate state in order to make decisions around where to make the next step, avoid looping inside the shape, etc. Less than ideal for this kata solution. There may be some neural net implementation of this possible that would be highly efficient. Especially for spaces with large area to perimeter ratios. This could have complexity of O(MxN)-c for a space of size MXN, where c is the number of internal 1's which a stateful boundary walk doesn't need to scan. An interesting concept but too complicated to optimize for this kata.

Next, I tried another approach which is what I decided to go with. This is a two stage approach. Map, and reduce.

Final Thoughts:
This is a fun and interesting problem. I think it is a good kata to demonstrate algorithmic problem solving. However, a full implementation takes a bit longer than typical interview kata often do. At least that is the case for me.

I found the number of shapes to be 14 for the data_small.txt and 573 for the data_large.txt.

I honestly have trouble counting the shapes in the small data set without printing it out and using crayons to highlight the boundaries. I no longer own a printer or crayons. Therefore I am not certain if I have an off by one error or something like that. I learned that crayon trick as a junior electrical engineer, as a way to highlight complex circuit groups in a less headache inducing way. Also, it like they say, "there's only 2 hard problems in computer science: naming things, cache invalidation, and off by one errors."

I hope you enjoyed reading my solution to this kata. I enjoyed producing it!

Also...
I added an Apache license to this and plan to share it on my github. Considering I did this for free that is more than fair in my opinion. If you differ in your opinion, please email me, the sole contributor to this solution. My contact is: David Gabriel contact@david-gabriel.com

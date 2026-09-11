# Leetcode_Day41
First Bad Version — Binary Search
Problem Overview
In this problem, we are given n product versions numbered from 1 to n. At some point, a version becomes bad, and every version released after it is also bad.

The goal is to find the first bad version while making as few calls as possible to the provided API:

java
boolean isBadVersion(int version)
For example, if version 4 is the first bad version, then versions 4, 5, ..., n are bad, while versions 1, 2, and 3 are good.

What I Learned
The main concept I learned from this problem is how to apply binary search to a monotonic condition.

A condition is monotonic when its result changes only once in one direction. In this problem, the versions follow this pattern:

text
Good, Good, Good, Bad, Bad, Bad
Because all versions after the first bad version are also bad, I do not need to check every version individually. Instead, I can repeatedly divide the search range in half.

How I Came Up With the Approach
Initially, a straightforward approach would be to check every version from 1 to n:

java
for (int version = 1; version <= n; version++) {
    if (isBadVersion(version)) {
        return version;
    }
}
However, this approach can make up to n API calls, which is inefficient for a large number of versions.

The important observation is that if a version is bad, then the first bad version must be at that version or somewhere before it. If a version is good, then the first bad version must be after it.

This leads naturally to binary search:

If mid is bad, move the upper boundary to mid.

If mid is good, discard the left half including mid and move the lower boundary to mid + 1.

Continue until both boundaries point to the same version.

Algorithm
Initialize low to 1 and high to n.

While low < high:

Calculate the middle version.

Check whether mid is bad.

If mid is bad, set high = mid.

Otherwise, set low = mid + 1.

When the loop ends, return low.

At that point, low and high are equal, and they represent the first bad version.

Java Implementation
java
/*
 * The isBadVersion API is defined in the parent class VersionControl.
 * boolean isBadVersion(int version);
 */

public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int low = 1;
        int high = n;

        while (low < high) {
            int mid = low + (high - low) / 2;

            if (isBadVersion(mid)) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }

        return low;
    }
}
Example Walkthrough
Suppose:

text
n = 5
First bad version = 4
The versions look like this:

text
1    2    3    4    5
Good Good Good Bad Bad
The search works as follows:

low = 1, high = 5

mid = 3

Version 3 is good.

The first bad version must be after 3.

Set low = 4.

mid = 4

Version 4 is bad.

The first bad version could be 4 or earlier.

Set high = 4.

Now low == high == 4

Return 4

Why high = mid?
When mid is bad, it may itself be the first bad version. Therefore, I must not remove it from the search range.

That is why I use:

java
high = mid;
Using high = mid - 1 could accidentally skip the correct answer.

When mid is good, it cannot be the first bad version, so it is safe to discard it:

java
low = mid + 1;
Complexity
Time complexity: 
O
(
log
⁡
n
)
O(logn)

Space complexity: 
O
(
1
)
O(1)

This is much more efficient than a linear search, which takes 
O
(
n
)
O(n) time.

Important Takeaways
Binary search is useful beyond sorted arrays.

It can be applied whenever the search space has a monotonic true/false pattern.

The pattern in this problem is:

text
false, false, false, true, true, true
When searching for the first valid or failing position, keep the possible answer inside the search range.

If the middle value satisfies the condition, move the right boundary to mid, not mid - 1.

If the middle value does not satisfy the condition, move the left boundary to mid + 1.

The condition low < high allows the algorithm to stop when both pointers converge on the answer.

Using low + (high - low) / 2 is safer than (low + high) / 2 because it helps avoid integer overflow.

Final Reflection
This problem helped me understand that binary search is not limited to finding an exact value in a sorted collection. It can also be used to find the first position where a condition becomes true.

The key skill is recognizing the monotonic pattern and carefully deciding which half of the search space can be eliminated after each API call.


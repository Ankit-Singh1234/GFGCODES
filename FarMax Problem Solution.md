# FarMax Problem Solution


🔎 The problem again

We want, for each index i, the farthest index j > i such that arr[j] > arr[i].

Brute force would be:

Fix i, scan rightward until the end.

Pick the farthest j.
👉 This is O(n²).

So the natural thought:
➡️ "Can I preprocess something so I don’t have to re-scan every time?"

✨ Step 1: Observe the condition

We need:
arr[j] > arr[i] for some j > i.

Instead of checking every j, notice:

If there exists at least one element greater than arr[i] in [k..n-1], then any j ≥ k could work.

So we only need to know whether the maximum in [k..n-1] is greater than arr[i].

This gives the idea of a suffix maximum array.

✨ Step 2: Define what helps quickly

If we precompute:

suffixMax[j] = maximum of arr[j...n-1]


Then:

To check if a valid j exists in [mid..n-1],
we just ask: is suffixMax[mid] > arr[i]?

This reduces a whole subarray scan into O(1).
So we can now binary search to find the farthest such j.

✨ Step 3: General Pattern

This suffix trick comes up in many problems:

Next greater element / farthest greater element → use suffixMax.

Next smaller element / farthest smaller element → use suffixMin.

Range queries (min/max/sum) → preprocess suffix/prefix arrays (or segment trees).

So the mental model is:

"I need to check some property (greater/smaller) in a range to the right. Instead of re-checking every time, can I preprocess the right side using suffix arrays?"

✅ How it clicks in practice

Try brute force first (O(n²)).

Notice repeated scanning of right side → inefficiency.

Ask yourself: "What one number summarizes this right subarray?"

For max condition → store suffix maximum.

For min condition → store suffix minimum.

For sums → store suffix sum.

Once you have that precomputed, binary search or two-pointers usually follow naturally.

👉 So suffixMax comes to mind because:

We are repeatedly asking about the right side of i.

"Maximum in the right side" is exactly the summary that answers whether a greater element exists.

## 🔎 Problem Statement
Given an array `arr` of size `n`, for each index `i` we want to find the **farthest index `j > i` such that `arr[j] > arr[i]`**.  
If no such index exists, return `-1` for that `i`.

---

## 🛠️ Approach

### 1. Brute Force (O(n²))
- For each index `i`, scan the array to the right to find the farthest index `j` such that `arr[j] > arr[i]`.
- Very slow for large `n`.

---

### 2. Optimized Approach (Suffix Maximum + Binary Search)

#### Step 1: Precompute `suffixMax`
`suffixMax[i] = maximum of arr[i...n-1]`

This allows us to quickly check if there exists a number greater than `arr[i]` in the range `[mid...n-1]`.

#### Step 2: Binary Search
- For each `i`, perform binary search on `[i+1...n-1]`.
- If `suffixMax[mid] > arr[i]`, then `mid` is a candidate.  
  Move right to try to find a farther index.
- Otherwise, move left.

#### Step 3: Store result
- Store the farthest valid index found.  
- If no valid index exists, result is `-1`.

---

## ✅ Example Walkthrough

Input:  
```
arr = [1, 2, 3, 1]
```

1. Build suffixMax:  
```
suffixMax = [3, 3, 3, 1]
```

2. Process each index:
- i=0, arr[0]=1 → farthest j=2
- i=1, arr[1]=2 → farthest j=2
- i=2, arr[2]=3 → no greater element → -1
- i=3, arr[3]=1 → no elements to the right → -1

Output:  
```
[2, 2, -1, -1]
```

---

## ⏱️ Complexity
- Building suffixMax: **O(n)**
- Binary search for each index: **O(log n)**
- For all indices: **O(n log n)**
- Much faster than brute force **O(n²)**.

---

## 💻 Code (C++)

```cpp
class Solution {
public:
    vector<int> farMax(vector<int>& arr) {
        int n = arr.size();
        vector<int> suffixMax(n);
        suffixMax[n-1] = arr[n-1];

        // Step 1: build suffixMax
        for (int i = n-2; i >= 0; i--) {
            suffixMax[i] = max(arr[i], suffixMax[i+1]);
        }

        vector<int> ans(n, -1);

        // Step 2: binary search for farthest j
        for (int i = 0; i < n; i++) {
            int l = i+1, r = n-1, res = -1;
            while (l <= r) {
                int mid = (l + r) / 2;
                if (suffixMax[mid] > arr[i]) {
                    res = mid;
                    l = mid + 1;  // try farther
                } else {
                    r = mid - 1;
                }
            }
            ans[i] = res;
        }

        return ans;
    }
};
```

---

## ✨ Key Idea
Whenever you need to answer queries about the **right side** of an array (greater/smaller), think of **suffix arrays** (suffixMax, suffixMin, suffixSum).  
They summarize the right half and allow faster searching using binary search or two-pointers.


Problem Restatement

We want, for each index i, the farthest index j > i such that arr[j] > arr[i].

🛠️ Step-by-Step Explanation
Step 1: Build suffixMax
vector<int> suffixMax(n);
suffixMax[n-1] = arr[n-1];

for (int i = n-2; i >= 0; i--) {
    suffixMax[i] = max(arr[i], suffixMax[i+1]);
}


suffixMax[i] stores the maximum value in the subarray arr[i...n-1].

Example:
If arr = [1, 2, 3, 1] →
suffixMax = [3, 3, 3, 1]

👉 Why we need this?
Because when we are at i, we don’t want to manually scan every j.
Instead, we can quickly check if there exists some j >= mid with arr[j] > arr[i].
That check is just: suffixMax[mid] > arr[i].

Step 2: Binary Search for Farthest j
for (int i = 0; i < n; i++) {
    int l = i+1, r = n-1, res = -1;
    while (l <= r) {
        int mid = (l + r) / 2;
        if (suffixMax[mid] > arr[i]) {
            res = mid;
            l = mid + 1;  // try farther
        } else {
            r = mid - 1;
        }
    }
    ans[i] = res;
}

Logic:

We search in the range [i+1 ... n-1] for a valid j.

mid = (l+r)/2 is our candidate position.

Case 1: suffixMax[mid] > arr[i]

This means in the range [mid ... n-1], there is at least one element greater than arr[i].

So mid is a valid candidate index.

But maybe we can find an even farther index, so we move right: l = mid + 1.

Case 2: suffixMax[mid] <= arr[i]

This means in [mid...n-1], all elements are <= arr[i].

So we must look left: r = mid - 1.

We store the farthest valid index found in res.
If no such index is found, res stays -1.

Step 3: Store result
ans[i] = res;


After binary search, res is the farthest index where arr[j] > arr[i].

If no such j exists, result is -1.

⏱️ Complexity

Building suffixMax: O(n)

Binary search for each index: O(log n)

For all indices: O(n log n)

Much faster than brute-force O(n²).

✅ Example Walkthrough

arr = [1, 2, 3, 1]

suffixMax = [3, 3, 3, 1]

Now process each i:

i=0, arr[0]=1
Binary search [1..3]:

mid=2 → suffixMax[2]=3 > 1 → valid, move right
→ result = 2 (farthest)
ans[0] = 2

i=1, arr[1]=2
Binary search [2..3]:

mid=2 → suffixMax[2]=3 > 2 → valid, move right
→ result = 2
ans[1] = 2

i=2, arr[2]=3
Binary search [3..3]:

mid=3 → suffixMax[3]=1 ≤ 3 → move left
→ no valid j
ans[2] = -1

i=3, arr[3]=1
No elements to the right → ans[3] = -1

Final output: [2, 2, -1, -1]

👉 In short:

suffixMax tells us if there is a larger element to the right.

Binary search finds the farthest such element efficiently.

✨ Step 1: Observe the condition

We need:
arr[j] > arr[i] for some j > i.

Instead of checking every j, notice:

If there exists at least one element greater than arr[i] in [k..n-1], then any j ≥ k could work.

So we only need to know whether the maximum in [k..n-1] is greater than arr[i].

This gives the idea of a suffix maximum array.

✨ Step 2: Define what helps quickly

If we precompute:

suffixMax[j] = maximum of arr[j...n-1]


Then:

To check if a valid j exists in [mid..n-1],
we just ask: is suffixMax[mid] > arr[i]?

This reduces a whole subarray scan into O(1).
So we can now binary search to find the farthest such j.

✨ Step 3: General Pattern

This suffix trick comes up in many problems:

Next greater element / farthest greater element → use suffixMax.

Next smaller element / farthest smaller element → use suffixMin.

Range queries (min/max/sum) → preprocess suffix/prefix arrays (or segment trees).

So the mental model is:

"I need to check some property (greater/smaller) in a range to the right. Instead of re-checking every time, can I preprocess the right side using suffix arrays?"

✅ How it clicks in practice

Try brute force first (O(n²)).

Notice repeated scanning of right side → inefficiency.

Ask yourself: "What one number summarizes this right subarray?"

For max condition → store suffix maximum.

For min condition → store suffix minimum.

For sums → store suffix sum.

Once you have that precomputed, binary search or two-pointers usually follow naturally.

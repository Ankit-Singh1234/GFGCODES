# FarMax Problem Solution

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

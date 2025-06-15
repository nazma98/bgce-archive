# 📚 Class 31 : Go Slice

## 🚀 Main Topics

- Slice কী?
- একটি Slice এ কয়টি অংশ থাকে?
- Slice এর Pointer, Length, এবং Capacity নির্ণয় করা
- বিদ্যমান Array থেকে Slice তৈরি
- বিদ্যমান Slice থেকে নতুন Slice তৈরি
- Slice Literal (সরাসরি ঘোষণা)
- `make()` দিয়ে Slice তৈরি (শুধু length)
- `make()` দিয়ে Slice তৈরি (length এবং capacity)
- খালি বা Nil Slice তৈরি
- Slice এ নতুন element যোগ করা (append)
- `append` করার সময় অভ্যন্তরীণ প্রক্রিয়া (Heap এবং Underlying Array)
- Underlying Array কীভাবে dynamic ভাবে বাড়ে
- কিছু মজার উদাহরণ এবং ইন্টারভিউ প্রশ্ন
- Variadic Functions

---

# 🧠 ১. Slice কী?

- **Slice** হলো Go এর একটি flexible data structure।
- এটি মূলত array এর উপরে নির্মিত একটি **dynamic view**।
- array এর মত হলেও, slice এর সাইজ পরিবর্তন করা যায় (বড় বা ছোট করা যায়)।

**মূল পয়েন্ট:**

- Slice, array নয়।
- Slice, array এর উপরে তৈরি হয়।

---

# 🔥 ২. একটি Slice এর কয়টি অংশ থাকে?

Slice মূলত একটি **struct** যা তিনটি অংশ নিয়ে গঠিত:

```go
struct Slice {
    pointer *T // points underlying array
    length  int // current elements number
    capacity int // maximum elements (until reallocation)
	}
```

Slice কে মূলত একটি array এর "window" হিসেবে ভাবা যেতে পারে।

---

# 🕵️‍♂️ ৩. POinter, lenth এবং capacity নির্ধারণ করার উপায়

নিম্নোক্ত built-in ফাংশন ব্যবহার করে Slice এর length এবং capacity বের করা যায়:

- `len(slice)` ➡️ Length
- `cap(slice)` ➡️ Capacity

উদাহরণ:

```go
s := arr[1:4] // index 1 to 3
fmt.Println(len(s)) // 3
fmt.Println(cap(s)) //  index 1 to end of the array
```

---

# 🏗 ৪. Array থেকে Slice তৈরি করা

```go
arr := [6]string{"This", "is", "a", "Go", "interview", "Questions"}
s := arr[1:4] // slice ["is", "a", "Go"]
```

- `pointer`: `arr` এর index 1 নির্দেশ করে
- `length`: 3 (index 1 to 3)
- `capacity`: 5 (index 1 to 5)

---

# 🔄 ৫. Slice থেকে নতুন Slice তৈরি

```go
s1 := s[1:2] // Slice "a"
```

- `s1` Slice টি একই Array এর একটি **view**!
- `s1` পরিবর্তন করলে `arr` এর মানও পরিবর্তিত হতে পারে।

---

# ✍️ 6. Slice Literal

একটি Slice তৈরি করতে সরাসরি মান ব্যবহার করা যায়, আলাদা করে Array তৈরি করার প্রয়োজন নেই।

```go
s2 := []int{3, 4, 7}
```

## এখানে Go automatically একটি underlying Array তৈরি করে।

# 🏗️ ৭. make() দিয়ে একটি Slice তৈরি করা

```go
s3 := make([]int, 3)
```

- `make()` দিয়ে ৩টি শূন্য মান (zero) সহ একটি Slice তৈরি করে। অর্থাৎ, ৩টি empty element থাকবে।
- `len = 3`, `cap = 3`

---

# 🏗️🏗️ ৮. `make()` দিয়ে একটি Slice তৈরি করা (length এবং capacity)

```go
s4 := make([]int, 3, 5)
```

- `len = 3`, কিন্তু capacity `cap = 5` পর্যন্ত হতে পারে reallocation এর আগ পর্যন্ত

---

# 🕳 9. Empty / Nil Slice Slice তৈরি

```go
var s5 []int
```

- `len = 0`, `cap = 0`
- Still valid! এতে নতুন element যোগ (append) করা যাবে।

---

# ➕ ১০. Slice এ নতুন element append করা

```go
s6 := append(s6, 1)
```

- Go নিজে থেকে underlying Array বড় করে।
- _অনেক বড় Array_ তৈরি এবং আগের element গুলো copy করার কাজও করে থাকে।

---

# 🧬 ১১. append করার সময় আসলে কী হয়?

যখন একটি Slice এর capacity পূর্ণ হয়:

- একটি **নতুন Array** (সাধারণত দ্বিগুণ সাইজের) তৈরি করা হয়।
- আগের Array এর সমস্ত elements নতুন Array তে কপি করা হয়।

এ কারণেই কখনও কখনও append করা দ্রুত মনে হয় এবং কখনও বড় মেমরি অপারেশন তৈরি করতে পারে।

---

# 📈 ১২. Underlying Array কিভাবে বৃদ্ধি পায়:

**যে প্যাটার্নে capacity পায় (Capacity Growth Pattern):** (simplified)

- Cap 1 ➡️ 2 ➡️ 4 ➡️ 8 ➡️ 16 ➡️ ...

এটি একটি optimization technique যা নিশ্চিত করে যেন append অপারেশনগুলো গড়ে `O(1)` সময়ে সম্পন্ন হয়।

---

# Go Slice বৃদ্ধি: `len` এবং `cap` এর dynamics বোঝা

Go Slice একটি শক্তিশালী এবং flexible data structure, যা dynamic Array এর মতো কাজ করে। এর একটি মূল বৈশিষ্ট্য হলো, যখন নতুন element যোগ করা হয়, তখন Slice নিজে নিজেই বৃদ্ধি পায়। Slice কীভাবে এবং কখন বৃদ্ধি পায়, আর এর মেমরি অ্যালোকেশনের পদ্ধতি বুঝলে অনেক efficient program design করা সম্ভব।

এখন আমরা Go Slice এর বৃদ্ধির পদ্ধতি বিশ্লেষণ করব:

- যদি Slice এর `len` এবং `cap` `1024`-এর কম হয়, তাহলে এটি সাধারণত দ্বিগুণ (2x) বৃদ্ধি পায়।
- `len` এবং `cap`, `1024` ছাড়িয়ে গেলে, এটি প্রায় 25% হারে বৃদ্ধি পায়।
- Slice কেন নির্দিষ্ট পরিমাণে (যেমন 1024 থেকে 1280) না বাড়িয়ে বড় ব্লকে (যেমন 1536) বাড়ে, সেটিও ব্যাখ্যা করা হবে।

## Slice Growth Overview

Go তে slice মূলত array এর উপরে ভিত্তি করে কাজ করে। যখন একটি slice এ নতুন element append করা হয়, তখন প্রয়োজনে Go একটি বড় array তৈরি করে এবং পুরানো element গুলো তাতে কপি করে। এই প্রসেসটি নির্ভর করে Go কিভাবে নতুন capacity নির্ধারণ করে এবং মেমরি allocate করে।

### ১২.১. ছোট Slice এর ক্ষেত্রে দ্বিগুণ বৃদ্ধি (`len(cap) < 1024`)

যখন slice ছোট থাকে (অর্থাৎ, এর `len` এবং `cap` দুটোই 1024 এর কম), তখন Go সাধারণত capacity **দ্বিগুণ** করে। এর মানে, যখন slice এ একটি element যোগ করা হয় এবং নতুন মেমরি প্রয়োজন হয়, তখন Go আগের capacity এর দ্বিগুণ আকারের একটি নতুন array তৈরি করে এবং পুরানো element গুলো সেখানে কপি করে। Slice এর `len` ১ বাড়বে, কিন্তু `cap` দ্বিগুণ হবে।

#### উদাহরণ:

```go
s := []int{1, 2, 3}
fmt.Println(len(s), cap(s)) // len: 3, cap: 3

s = append(s, 4)
fmt.Println(len(s), cap(s)) // len: 4, cap: 6

s = append(s, 5)
fmt.Println(len(s), cap(s)) // len: 5, cap: 6
```

- প্রথমে, slice এর `len` ৩ এবং `cap` ৩ থাকে।
- চতুর্থ element যোগ করার সময়, slice এর capacity ৩ থেকে দ্বিগুণ হয়ে ৬ হয়ে যায়।
- পরবর্তী অ্যাপেন্ডে, slice এর capacity ৬ ই থাকবে কারণ `len` < `cap`।

### ১২.২. বড় Slice এর ক্ষেত্রে ২৫% বৃদ্ধি (`len(cap) >= 1024`)

যখন slice এর `len` এবং `cap` 1024 বা তার বেশি হয়, তখন Go দ্বিগুণের পরিবর্তে বর্তমান capacity এর **২৫%** বৃদ্ধি করে। এই কৌশলটি ঘন ঘন মেমরি reallocation এড়াতে এবং অপ্রয়োজনীয় মেমরি অপচয় কমাতে সহায়তা করে।

#### উদাহরণ:

```go
s := make([]int, 1024)  // len: 1024, cap: 1024
fmt.Println(len(s), cap(s))

s = append(s, 1025) // len: 1025, cap: 1280 (1024 + 25% of 1024)
fmt.Println(len(s), cap(s))

s = append(s, 1300) // len: 1300, cap: 1600 (1280 + 25% of 1280)
fmt.Println(len(s), cap(s))
```

- শুরুতে, আমরা length এবং capacity 1024 সহ একটি slice তৈরি করি।
- পরের element append করলে slice এর capacity 1024 থেকে 1280 হয়, যা 1024 এর সাথে ২৫% যোগ করে পাওয়া যায়।
- আরেকটি element append করলে capacity 1600 হয় (1280 এর সাথে ২৫% যোগ করে)।

### ১২.৩. মেমরি ব্লকের ভূমিকা (যেমন, 1536 ক্ষমতার Slice)

যখন slice এর `len` এবং `cap` 1024 এর কাছাকাছি বা তার বেশি হয়, তখন Go সর্বদা নিখুঁত গণনা অনুযায়ী মেমরি বরাদ্দ করে না। এর পরিবর্তে এটি সিস্টেম মেমরি বরাদ্দের ধরণ অনুযায়ী **Optimal Memory Block** ব্যবহার করে।

উদাহরণস্বরূপ, যদি একটি slice এর capacity 1024 এর কাছাকাছি থাকে, পরবর্তী বরাদ্দ সরাসরি 256 যোগ করে 1280 না হয়ে, এর থেকে বড় 1536 এর মতো ব্লকে হতে পারে। এটি বড় মেমরি ব্লকের জন্য আরও কার্যকর মেমরি ব্যবহারে সহায়তা করে।

#### কেন 1536 এর পরিবর্তে 1280?

এটি মূলত **Hardware Memory Alignment** এর উপর নির্ভর করে। 1536 সংখ্যাটি বেছে নেওয়া হয় কারণ এটি সাধারণত 2-এর গুণিতক আকারের মেমরি ব্লকের সাথে ভালোভাবে মিলে যায় এবং আধুনিক CPU এবং মেমরি সিস্টেমের জন্য optimize করা। মেমরি অ্যালোকেশন সাধারণত সিস্টেমের memory page size বা cache line এর সাথে সামঞ্জস্য রেখে বড় ব্লকে করা হয়, যা মেমরি অ্যাক্সেসকে আরও ইফিশিয়েন্ট করে তোলে।

#### উদাহরণ (memory alignment):

```go
s := make([]int, 1024) // len: 1024, cap: 1024
fmt.Println(len(s), cap(s)) // 1024, 1024

s = append(s, 1025) // len: 1025, cap: 1536 (next optimal block size)
fmt.Println(len(s), cap(s)) // 1025, 1536
```

- 1024 থেকে 1536 এ capacity বৃদ্ধি হয়, কারণ 1536 একটি বেটার মেমরি ব্লক যা সিস্টেমের ইফিশিয়েন্ট মেমরি অ্যালোকেশন করে।

### ১২.৪. কেন এমন হয়?

**efficiency considerations** Go সরাসরি 256 করে বৃদ্ধি করে না (যেমন আমরা মনে করি 1024 থেকে 1280)। কারণ এটি ইফিশিয়েন্সি বৃদ্ধির জন্য গুরুত্বপূর্ণ। এই অ্যালোকেশন স্ট্র্যাটেজির ফলে ঘন ঘন রি-অ্যালোকেশন এড়ানো যায় এবং অপ্রয়োজনীয় মেমরি অপচয় কমে যায়। বরং বড় ব্লকে (1536) মেমরি বরাদ্দ করে Go রানটাইম নিশ্চিত করে Sliceে পর্যাপ্ত capacity আছে আরও element এপেন্ড করার জন্য এবং খুব তাড়াতাড়ি Sliceকে বাড়াতে হবে না।

এর ফলে আরও ভালো performance পাওয়া যায়, বিশেষ করে যখন slice দ্রুত বৃদ্ধি পায়।

## Conclusion

Go-এর slice বৃদ্ধি কৌশল অনেক ইফিশিয়েন্ট কোড লিখা সম্ভব। ছোট সাইজের slice এর ক্ষেত্রে, Go capacity দ্বিগুণ করে যাতে কম রি-অ্যালোকেশনে আরও element ধারণ করতে পারে। বড় slice (1024 এবং এর বেশি) হলে, এটি capacity ২৫% বৃদ্ধি করে এবং মাঝে মাঝে অপ্টিমাল মেমরি ব্লকের সাথে সামঞ্জস্য রাখে। এই পদ্ধতিটি slice কে দ্রুত এবং মেমরি সাশ্রয়ী করে তোলে।

---

# 🤯১৩. কিছু ইন্টারেস্টিং ইন্টারভিউ প্রশ্নের উদাহরণ

### ⚡ Same Underlying Array Trick

```go
var x []int
x = append(x, 1)
x = append(x, 2)
x = append(x, 3)

y := x
x = append(x, 4)
y = append(y, 5)

x[0] = 10
fmt.Println(x)
fmt.Println(y)
```

-x এবং y একই backing array শেয়ার করে

- একটিতে পরিবর্তন (mutation) করলে দুটোরই মান পরিবর্তিত হতে পারে।

কিন্তু `cap` অতিক্রম করে অ্যাপেন্ড করলে, তারা আলাদা Array তে বিভক্ত হতে পারে।

# 🛠 ১৪. Variadic Functions

কোন ফাংশন `...` (ellipsis) operator ব্যবহার করে অসংখ্য argument receive করতে পারে।

```go
func variadic(numbers ...int) {
    fmt.Println(numbers)
}

variadic(2, 3, 4, 6, 8, 10)
```

এখানে `numbers` কিন্তু একটি **slice**!

---

# 🧠 RAM এ Slice এর visualization (arr এবং s এর জন্য)

```
Array arr (indexes):
[0] "This"
[1] "is"  <- s.ptr points here
[2] "a"
[3] "Go"
[4] "interview"
[5] "Questions"

Slice s:
- ptr = &arr[1]
- len = 3 ("is", "a", "Go")
- cap = 5 (from "is" to "Questions")
```

Memory Visualization:

```
+----+--+-+--+---------+---------+
|This|is|a|Go|interview|Questions|
+----+--+-+--+---------+---------+
       ^ ^  ^
     s[0] s[1] s[2]
```

---

# 📄 Full Code with Detailed Comments

```go
package main

import "fmt"

func main() {
	// Create an array of strings
	arr := [6]string{"This", "is", "a", "Go", "interview", "Questions"}
	fmt.Println(arr)

	// Create a slice from array indexes 1 to 3 (exclusive of 4)
	s := arr[1:4]
	fmt.Println(s) // [is a Go]

	// Create a slice from a slice
	s1 := s[1:2]
	fmt.Println(s1) // [a]
	fmt.Println(len(s1)) // 1
	fmt.Println(cap(s1)) // 4 (capacity depends on the underlying array)

	// Slice literal
	s2 := []int{3, 4, 7}
	fmt.Println("slice", s2, "lenght:", len(s2), "capacity:", cap(s2))

	// make() function with length only
	s3 := make([]int, 3)
	s3[0] = 5
	fmt.Println(s3)
	fmt.Println(len(s3))
	fmt.Println(cap(s3))

	// make() function with length and capacity
	s4 := make([]int, 3, 5)
	s4[0] = 5
	fmt.Println(s4)
	fmt.Println(len(s4))
	fmt.Println(cap(s4))

	// Empty slice
	var s5 []int
	fmt.Println(s5) // []

	// Appending elements to empty slice
	var s6 []int
	s6 = append(s6, 1)
	fmt.Println(s6) // [1]

	var s7 []int
	s7 = append(s7, 1, 2, 3)
	fmt.Println(s7, len(s7), cap(s7)) // [1 2 3] 3 3

	// Interview question: Sharing underlying array
	var x []int
	x = append(x, 1)
	x = append(x, 2)
	x = append(x, 3)

	y := x
	x = append(x, 4)
	y = append(y, 5)

	x[0] = 10

	fmt.Println(x) // [10 2 3 5]
	fmt.Println(y) // [10 2 3 5]

	// Another interview question
	slc := []int{1, 2, 3, 4, 5}
	slc = append(slc, 6)
	slc = append(slc, 7)

	slcA := slc[4:]

	slcY := changeSlice(slcA)

	fmt.Println(slc)  // [1 2 3 4 10 6 7]
	fmt.Println(slcY) // [10 6 7 11]
	fmt.Println(slc[0:8]) // [1 2 3 4 10 6 7 11]

	// Variadic function call
	variadic(2, 3, 4, 6, 8, 10)
}

// Function that changes the slice passed
func changeSlice(a []int) []int {
	a[0] = 10
	a = append(a, 11)
	return a
}

// Variadic function that takes multiple integers
func variadic(numbers ...int) {
	fmt.Println(numbers)
	fmt.Println(len(numbers))
	fmt.Println(cap(numbers))
}
```

[**Author:** @ifrunruhin12, @nazma98
**Date:** 2025-05-01 - 2025-05-18
**Category:** interview-qa/class-wise
]

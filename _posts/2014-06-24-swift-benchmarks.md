---
layout: post
title: Some Swift benchmarks
categories: [programming]
tags:
- programming
- swift
---

There has been some talk of Apple's new [Swift](https://developer.apple.com/swift/) language being slow without the use of safety-compromising compiler optimizations ([here](http://stackoverflow.com/questions/24101718/swift-performance-sorting-arrays) and [here](http://stackoverflow.com/questions/24102609/why-swift-is-100-times-slower-than-c-in-this-image-processing-test), for example), so I decided to do some experiments myself. I'll update this post as I perform more benchmarks.

I wrote an equivalent quicksort implementation in Swift and C. The implementation is nothing fancy, and exactly follows [that on quicksort's Wikipedia page](http://en.wikipedia.org/wiki/Quicksort#Algorithm). The test consists of sorting a reversed array of 10,000 consecutive integers.

#### Swift

{% highlight cpp %}
func partition(let array: Array<Int>,
               let left: Int,
               let right: Int) -> Int {

	let pivotIndex = right
	let pivotValue = array[pivotIndex]
	
	array[pivotIndex] = array[right]
	array[right] = pivotValue;
	
	var storeIndex = left;
	
	for i in left..right {
		if array[i] <= pivotValue {
			let tmp = array[i]
			array[i] = array[storeIndex]
			array[storeIndex++] = tmp
		}
	}
	
	let tmp = array[right]
	array[right] = array[storeIndex]
	array[storeIndex] = tmp
	
	return storeIndex
}

func quicksort(let A: Array<Int>, let i: Int, let k: Int) {
	if i < k {
		let p = partition(A, i, k)
		
		if p > 0 {
			quicksort(A, i, p - 1)
		}
		
		if p < k {
			quicksort(A, p + 1, k)
		}
	}
}

let SIZE = 10000
var A = Int[](count: SIZE, repeatedValue: 0)

for i in 0..SIZE {
	A[i] = SIZE - i
}

quicksort(A, 0, SIZE - 1)
{% endhighlight %}

#### C

{% highlight cpp %}
#include <stdio.h>

static size_t partition(int *array,
                        const size_t left,
                        const size_t right);

static void quicksort(int *A,
                      const size_t i,
                      const size_t k);

static size_t partition(int *array,
                        const size_t left,
                        const size_t right)
{
	const size_t pivot_index = right;
	const int pivot_value = array[pivot_index];

	array[pivot_index] = array[right];
	array[right] = pivot_value;
	
	size_t store_index = left;
	
	for (size_t i = left; i < right; i++) {
		if (array[i] <= pivot_value) {
			const int tmp = array[i];
			array[i] = array[store_index];
			array[store_index++] = tmp;
		}
	}

	int tmp = array[right];
	array[right] = array[store_index];
	array[store_index] = tmp;
	
	return store_index;
}

static void quicksort(int *A,
                      const size_t i,
                      const size_t k)
{
	if (i < k) {
		const size_t p = partition(A, i, k);
		
		if (p > 0)
			quicksort(A, i, p - 1);
			
		if (p < k)
			quicksort(A, p + 1, k);
	}
}

int main(void)
{
#define SIZE 10000
	int A[SIZE];
	
	for (size_t i = 0; i < SIZE; i++) {
		A[i] = SIZE - i;
	}
	
	quicksort(A, 0, SIZE - 1);
#undef SIZE
}
{% endhighlight %}

#### Results

<pre>
$ xcrun swift -i -O3 -emit-executable quicksort.swift
$ time ./quicksort
<b>
real	0m14.236s
user	0m14.218s
sys	0m0.012s</b>
$ xcrun swift -i -Ofast -emit-executable quicksort.swift
$ time ./quicksort
<b>
real	0m0.076s
user	0m0.069s
sys	0m0.004s</b>
$ gcc -std=c99 -O3 -o quicksort quicksort.c
$ time ./quicksort
<b>
real	0m0.042s
user	0m0.039s
sys	0m0.002s</b>
</pre>

  | **Language**    | **Time (s)** | **Ratio** |
  | ----------------| ------------ | --------- |
  | Swift (`-O3`)   | 14.236       | 339.      |
  | Swift (`-Ofast`)| 0.076        | 1.81      |
  | C (`-O3`)       | 0.042        | 1.00      |
    
So it looks like there is substance to these claims. Consistently using `-Ofast` doesn't seem like a viable alternative either, since it removes many of the safety features and therefore undermines the motivation for using the language in the first place. Consider the following, for example:

{% highlight cpp %}
let v: Int[] = [1,2,3]
println(v[3])  // should be an out-of-bounds error
{% endhighlight %}

**Output with `-O3`:**
<pre>
fatal error: Array index out of range
</pre>

**Output with `-Ofast`:**
<pre>
0
</pre>

The `-Ofast`-compiled program is completely silent about the fact that the array was indexed out of bounds.

We can see where the speed impediment comes from by looking at the generated assembly of a Swift program, and comparing it to that of an equivalent C program (using the `-S` switch). Consider the following simple program:

#### Swift

{% highlight cpp %}
func increment(x: Int) -> Int {
	return x + 1
}

increment(42)
{% endhighlight %}

#### C

{% highlight cpp %}
static int increment(int x)
{
	return x + 1;
}

int main(void) {
	increment(42);
}
{% endhighlight %}

The generated assembly (using `-O3`):

#### Swift

<pre>
	.section	__TEXT,__text,regular,pure_instructions
	.globl	__TF6simple9incrementFSiSi
	.align	4, 0x90
__TF6simple9incrementFSiSi:
	pushq	%rbp
	movq	%rsp, %rbp
	incq	%rdi
	jo	LBB0_2
	movq	%rdi, %rax
	popq	%rbp
	retq
LBB0_2:
	ud2

	.globl	_main
	.align	4, 0x90
_main:
	.cfi_startproc
	pushq	%rbp
Ltmp3:
	.cfi_def_cfa_offset 16
Ltmp4:
	.cfi_offset %rbp, -16
	movq	%rsp, %rbp
Ltmp5:
	.cfi_def_cfa_register %rbp
	pushq	%r14
	pushq	%rbx
Ltmp6:
	.cfi_offset %rbx, -32
Ltmp7:
	.cfi_offset %r14, -24
	movq	%rsi, %r14
	movl	%edi, %ebx
	callq	__TFSsa6C_ARGCVSs5Int32
	movl	%ebx, (%rax)
	callq	__TFSsa6C_ARGVGVSs13UnsafePointerVSs7CString_
	movq	%r14, (%rax)
	xorl	%eax, %eax
	popq	%rbx
	popq	%r14
	popq	%rbp
	retq
	.cfi_endproc

	.linker_option "-lswift_stdlib_core"
	.section	__DATA,__objc_imageinfo,regular,no_dead_strip
L_OBJC_IMAGE_INFO:
	.long	0
	.long	0


.subsections_via_symbols
</pre>

#### C

<pre>
	.section	__TEXT,__text,regular,pure_instructions
	.globl	_main
	.align	4, 0x90
_main:                                  ## @main
	.cfi_startproc
## BB#0:
	pushq	%rbp
Ltmp2:
	.cfi_def_cfa_offset 16
Ltmp3:
	.cfi_offset %rbp, -16
	movq	%rsp, %rbp
Ltmp4:
	.cfi_def_cfa_register %rbp
	xorl	%eax, %eax
	popq	%rbp
	retq
	.cfi_endproc


.subsections_via_symbols
</pre>

No wonder the Swift variant is slower; that's a lot of code to do something so simple. Personally, I have no problem with Swift being noticeably slower than C -- in fact I would be surprised if it weren't -- I *do*, however, have a problem with it being **orders of magnitude** slower. In fairness, Swift is still in its early stages, and I'm confident that this issue will be resolved in (hopefully the near) future.


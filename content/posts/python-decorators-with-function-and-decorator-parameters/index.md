+++
date = '2025-05-16T21:17:13+02:00'
draft = false
title = 'Python Decorators With Function and Decorator Parameters'
tags=["python"]
+++

```py
def modify_multiply_decorator(*args, **kwargs): # coeff=3

	def dec(multiply_func):

		def inner(*nums): # (2, 290)
			coefficient = kwargs["coefficient"] # 3
			result = multiply_func(*nums) # 2 * 290
			modified_result = coefficient * result # 3 * 580

			return modified_result # 1740

		return inner

	return dec
```

```py
@modify_multiply_decorator(coefficient=3)
def multiply(*nums):

	return nums[0] * nums[1]

print(multiply(2,290))
```

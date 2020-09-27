# 数学

### Factoril 阶乘
在数学中，用n！表示的非负整数n的阶乘是所有小于或等于n的正整数的乘积。
`5! = 5 * 4 * 3 * 2 * 1 = 120`
```js
/*
* @param {number} num
*/
//尾递归的优化
function factoril_1(i,a) {
	a = a||1;
	if(i<2) {
		return a;
	}
	return factoril_1(i-1,a*i);
}
//闭包
function factoril_2(num) {
	if(num<=1) {
		return 1;
	}else{
		return num*factoril_2(num-1);
	}
}
//for循环
function factoril_3(num) {
	for(let i=num-1;i>=1;i--) {
		num *= i;
	}
	return num;
}
//while循环
function factoril_4(num) {
	let result = num;
	while(num>1) {
		num--;
		result *= num;
	}
	return result;
}
```
##### Fibonacci Number
在数学中，斐波那契数列是以下整数序列中的数字，其特征在于前两个数字之后的每个数字都是前两个数字的和：`0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ...`
边长为连续斐波纳契数的正方形平铺

![alt](https://camo.githubusercontent.com/f653fca3a6fcf1733d0b19c3ddb37622926b42e7/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f642f64622f333425324132312d4669626f6e61636369426c6f636b732e706e67)

斐波那契螺旋：通过绘制连接斐波那契平铺中正方形的相对角的圆弧而创建的金色螺旋的近似值； [4]该三角形使用大小为1、1、2、3、5、8、13和21的正方形.

![alt](https://camo.githubusercontent.com/e1127247ec2da22f21e548352a86e7180f10d7bf/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f322f32652f4669626f6e6163636953706972616c2e737667)

```js
/**
 * Return a fibonacci sequence as an array.
 *以数组形式返回斐波那契数列
 * @param n
 * @return {number[]}
 */
 function fibonacci(n) {
 	const fibSequence = [1];
 	let currentValue = 1;
 	let previousValue = 0;
 	if(n===0) {
 		return fibSequence
 	}
 	let iterationCounter = n-1;
 	while(iterationCounter) {
 		currentValue += previousValue;
 		previousValue = currentValue -previousValue;
 		fibSequence.push(currentValue);
 		iterationCounter--;
 	}
 	return fibSequence;
 }
```
```js
/**
 * Calculate fibonacci number at specific position using Dynamic Programming approach.
 *使用动态编程方法计算特定位置的斐波那契数。
 * @param n
 * @return {number}
 */
 function fibonacciNth(n) {
 	let currentValue = 1;
 	let previousValue = 0;

	if (n === 1) {
		return 1;
	}
  	let iterationsCounter = n - 1;
  	while (iterationsCounter) {
  		currentValue += previousValue;
  		previousValue = currentValue - previousValue;
  		iterationsCounter--;;
  	}
  	return currentValue;
}
function fibo()	{
    let [pre,curr] = [0,1];
    for(;;)	{
        yield curr;
        [pre,curr] = [curr,pre + curr];
    }
}
for(let i of fibo())
{
    if(i > 10000)
    {
        break;
    }
    console.log(i);
}
```
```js
/**
 * Calculate fibonacci number at specific position using closed form function (Binet's formula).
 * 使用封闭形式函数（Binet公式）计算特定位置的斐波那契数。
 *
 * @param {number} position - Position number of fibonacci sequence (must be number from 1 to 75).
 * @return {number}
 */
 function fibonacciClosedForm(position) {
 	cosnt topMaxValidPosition = 70;
 	//check that position is valid
 	if(position<1 || position>topMaxValidPosition) {
 		throw new Error(`Can't handle position smaller than 1 or greater than ${topMaxValidPosition}`)
 	}
 	//calculate √5 to re-use it in futher formulas
 	const sqrt5 = Math.sqrt(5);
 	//calculate φ constant (≈1.61803)
 	const phi = (1+sqrt5)/2;
 	//calculate fibonacci number using binet's formula
 	return Math.floor((phi ** position)/sqrt5+0.5)
 } 
```
##### Primality Test
质数（或素数）是大于1的自然数，不能通过将两个较小的自然数相乘而形成。 大于1的非质数自然数称为复合数。 例如，5是质数，因为将其写为乘积的唯一方式是1×5或5×1，涉及5本身。 但是，6是复合的，因为它是两个都小于6的两个数字（2×3）的乘积。

![alt](https://camo.githubusercontent.com/8da56db2bd124541a05194f940b61c974e69968b/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f662f66302f5072696d65732d76732d636f6d706f73697465732e737667)

素数测试是一种用于确定输入数字是否为素数的算法。 在数学的其他领域中，它用于加密。 与整数分解不同，素数测试通常不给出素数，而仅说明输入数是否为素数。 分解被认为是一个计算难题，而素数测试相对容易（其运行时间是输入大小的多项式）。
```js
/**
 * @param {number} number\
 *排除法
 * @return {boolean}
 */
 export default function trialDivision(number) {
 	//check if number is integer
 	if(number%1!==0) {
 		return false;
 	}
 	if(number<=1) {
 		//if number is less than one then it isn't prime by definition
 		return false;
 	}
 	if(number<=3) {
 		//all number from 2 to 3 are prime
 		return true;
 	}
 	if(number%2===0) {
 		return false;
 	}
 	//if there is no dividers up to square root of n then there is no higher dividers as well
 	const dividerLimit = Math.sqrt(number);
 	for(let divider = 3; divider<=dividerLimit; divider += 2) {
 		if(number % divider ===0 ) {
 			return false;
 		}
 	}
 	return true;
 }

```
##### Euclidean algorithm 欧几里得算法-计算最大公约数
在数学中，欧几里得算法是计算两个数字的最大公约数（GCD）的有效方法，该最大公约数可将两个数除而无余数。
欧几里得算法基于这样的原理：如果将较大的数字替换为较小的数字，则两个数字的最大公约数不变。 例如，21是252和105的GCD（因为252 = 21×12和105 = 21×5），相同的数字21也是105和252-105 = 147的GCD。 对这两个数字中的一个进行重复，重复此过程，将得出连续较小的数字对，直到两个数字相等为止。 发生这种情况时，它们就是原始两个数字的GCD。
通过反转步骤，可以将GCD表示为两个原始数字的总和，每个数字乘以一个正整数或负整数，例如21 = 5×105 +（-2）×252。 以这种方式表达的被称为贝祖定理。

![alt](https://camo.githubusercontent.com/62ad940498abce3d7ef639978bb694d4bfae149c/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f332f33372f4575636c6964253237735f616c676f726974686d5f426f6f6b5f5649495f50726f706f736974696f6e5f325f332e706e67)

欧几里得的方法用于找到两个起始长度BA和DC的最大公约数（GCD），两者均定义为共同“单位”长度的倍数。长度DC较短，用于'测量'BA，但仅一次，因为剩余的EA小于DC。 EA现在测量（两倍）较短的长度DC，其余FC比EA短。然后，FC测量（三倍）长度EA。 因为没有余数，所以该过程以FC为GCD结束。 在右边的尼科马修斯的例子中，数字49和21导致GCD为7（源自希思1908：300）。
一个24 x 60的矩形覆盖有十个12x12的正方形拼贴，其中12是GCD为24和60。更一般而言，a x b矩形可以覆盖边长为c的正方形拼贴 仅当c是a和b的公约数时。
基于减法的欧几里得算法动画。 初始矩形的尺寸为a = 1071和b =462。大小为462×462的正方形放置在其中，剩下462×147矩形。 将该矩形用147×147正方形平铺，直到剩下21×147矩形，然后再用21×21正方形平铺，不留任何未覆盖区域。 最小的正方形大小为21，即GCD为1071和462。

![alt](https://camo.githubusercontent.com/eaf4fa99b80cbb864f56a4f180231887b684f360/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f312f31632f4575636c696465616e5f616c676f726974686d5f313037315f3436322e676966)

```js
/**
 * Recursive version of Euclidean Algorithm of finding greatest common divisor (GCD).
 *查找最大公约数（GCD）的欧几里德算法的递归版本。
 * @param {number} originalA
 * @param {number} originalB
 * @return {number}
 */
 export default function euclideanAlgorithm(originalA,originalB) {
 	//make input numbers positive
 	const a = Math.abs(originalA);
 	const b = Math.abs(originalB);
 	//to make algorithm work faster instead of subtracting one number from the other
 	//we may use modulo operation
 	return (b===0) ? a:euclideanAlgorithm(b, a%b)
 }
```
```js
/**
 * Iterative version of Euclidean Algorithm of finding greatest common divisor (GCD).
 *查找最大公约数（GCD）的欧几里德算法的迭代版本。
 * @param {number} originalA
 * @param {number} originalB
 * @return {number}
 */
 export default function euclideanAlgorithmItreative(originalA,originalB) {
 	// subtract one number from another until both numbers would become the seam.
 	// this will be out GCD ,also quit the loop if one of the numbers is zero.
 	while(a&&b&&a!==b) {
 		[a, b]= a>b ? [a-b,b] : [a, b-a];
 	}
 	//return the number that is not equal to zero since the last subtraction(it will be a GCD)
 	return a || b;
 }
```

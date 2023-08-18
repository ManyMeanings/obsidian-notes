```swift
// 字符串插值
print("hello \(2 + 3) world") // hello 5 world

// 声明常量
let a: Int = 1
// 声明变量
var a: Int = 1

// Optianal
var a: String? = nil // a 可为空
a = "hello world"
print(a) // Optional("hello world")
// 确保 a 不为空时用 ! 打开，或者增加条件判断
print(a!) // "hello world"

// 范围运算符
a...b // [a, b]
a..<b // [a, b)
...b // <=b

// 字典
let eggTimes = ["Soft": 5, "Medium": 7, "Hard": 12]
print(eggTimes["Soft"]!) // 5

// 生成1个0-5之间的随机整数
Int.random(in: 0 ... 5)
Int.random(in: 0 ..< 6)

// 函数
func funcName(text: String) {
	// do something
	print(text)
}
funcName(text: "hello world")

// 条件语句
if a == b {
	// do something
} else if a == c {
	// do something
} else {
	// do something
}

// switch 语句
switch a {
	case "1":
		// do something
	case "2":
		// do something
	default:
		// do something
}
```
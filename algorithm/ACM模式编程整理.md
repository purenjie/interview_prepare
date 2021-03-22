在有多行数据输入的情况下，一般这样处理

```java
static Scanner sc = new Scanner(System.in);

// 循环判断是否还有输入
// sc.hasNext() 或 sc.hasNextInt() 或 sc.hasNextDouble() 或 sc.hasNextLine()
while(sc.hasNext()) 
    
// 读取格式
int n = sc.nextInt(); 
String s = sc.next();
double t = sc.nextDouble();
String s = sc.nextLine(); // 读取一行
```

- Case 1

输入数据有多组，每组占 2 行，第一行为一个整数 N，指示第二行包含 N 个实数

```
Sample Input
4 
56.9  67.7  90.5  12.8 
5 
56.9  67.7  90.5  12.8 
```

```java
import java.util.Scanner;
 
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		while (sc.hasNext()) { // 每一组数据
			int n = sc.nextInt(); // 接收第一个整数
			for (int i = 0; i < n; i++) {
				double a = sc.nextDouble(); // 接收下一行 N 个数
            }
        }
    }
}
```

- Case 2

输入数据有多行，第一行是一个整数 n，表示测试实例的个数，后面跟着 n 行，每行包括一个由字母和数字组成的字符串

```
Sample Input  
2
asdfasdf123123asdfasdf
asdf111111111asdfasdfasdf
```

```java
import java.util.Scanner;
 
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int n = sc.nextInt(); // 接收第一个整数
        // int n = Integer.parseInt(sc.nextLine());
		for (int i = 0; i < n; i++) {
			String str = sc.next(); // 接收 N 行字符串
		}
	}
}
```


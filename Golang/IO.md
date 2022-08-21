## 标准输入输出

### 使用fmt读取数据

```go
package main
import "fmt"
import "io"

// 读取以空白符分割的值返回到地址中进行修改，换行视为空白符
// func Scan(a ...interface{}) (n int, err error) 
// func Scanf(format string, a ...interface{}) (n int, err error) 
// 在遇到换行时才停止扫描。最后一个数据后面必须有换行或者到达EOF
// func Scanln(a ...interface{}) (n int, err error)

func main() {
    var input string
    _, err := fmt.Scan(&input)
    for err != io.EOF {
        _, err = fmt.Scan(&input)
    }
}


func main() {
    var a, b int
    for { // 循环获取输入, 空白分割, 换行视为空白
        if _, err := fmt.Scan(&a,&b); err != io.EOF {
            fmt.Println(a + b)
        } else {
            break
        }
    }
}
```

### 使用bufio读取数据

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "io"
)

// func (b *Reader) ReadString(delim byte) (string, error)

var inputReader *bufio.Reader
var input string
var err error

func main() {
    inputReader = bufio.NewReader(os.Stdin)
    input, err = inputReader.ReadString('\n')
    if err == nil || err == io.EOF {
        fmt.Printf("The input was: %s\n", input)
    }
}

func main() {
    input := bufio.NewScanner(os.Stdin) //创建并返回一个从os.Stdin读取数据的Scanner
    for input.Scan(){
	// Scan方法获取当前位置的token（该token可以通过Bytes或Text方法获得），
	// 并让Scanner的扫描位置移动到下一个token。
	// 当扫描因为抵达输入流结尾或者遇到错误而停止时，本方法会返回false
        nums := strings.Split(input.Text(), " ") //分割字符串
        fmt.Println(nums)
    }
}
```

> `bufio.Reader`读取数据时会把分隔符一起读到数据中，如果读取时遇到错误（如io.EOF）,则返回该错误
>

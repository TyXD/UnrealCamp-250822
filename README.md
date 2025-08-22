# [내일배움캠프 언리얼 5기] 객체지향적 설계

## 1. 학습 키워드

>## 객체 지향적 설계

>## SOLID 원칙

## 2. 학습 내용

## 객체지향적 설계

객체 지향적으로 코드를 구현하는것은 상당히 중요합니다.

> 1. 대부분 라이브러리 및 오픈소스는 객체지향적으로 설계되어 있습니다.

> 2. 좋은 설계로 구현된 코드는 개발 시간을 단축할 수 있습니다.

> 3. 좋은 설계로 구현된 코드는 기능 변경에 유연하게 대응할 수 있습니다.

### 응집도

응집도는 클래스 또는 모듈 내부의 구성요소들이 얼마나 밀접하게 관련되어 있는지를 나타냅니다.

클래스 내부에 관련 없는 기능들이 포함되어 있으면, 변경이 자주 발생하고, 확장하기도 쉽지 않습니다.

### 응집도가 낮은 경우

서로 관련없는 기능들이 하나의 클래스에 포함된 경우를 이야기 합니다.

예를 들어 프로그래머가 배달 매장의 배달 관리 코드를 작성을 했는데 다음과 같은

> 1. 음식 배달

> 2. 웹사이트 디자인

> 3. 매장 마케팅

> 4. 매장 창고 관리

등이 기능들이 한 클래스에 포함되게 작성했다면 

배달과 관련된 기능들이 클래스 내부에 있기에 응집도가 낮다고 볼 수 있습니다.

### 응집도가 높은 경우

서로 관련 있는 모듈들만 모아 하나의 클래스로 만든 경우 응집도가 높다고 이야기 합니다.

> 1. 배달 경로 확인

> 2. 주문 고객 요청 사항

> 3. 배달 예상 시간

이와 같은 기능들이 한 클래스에 포함되게 작성되었다면 이는 응집도가 높다고 볼 수 있습니다.

### 응집도 실제 예시

>### 응집도가 낮은 코드

```cpp
#include <iostream>
#include <string>
#include <algorithm> // for transform
using namespace std;

class Utility {
public:
    void printMessage(const string& message) {
        string upperMessage = message;
        transform(upperMessage.begin(), upperMessage.end(), upperMessage.begin(), ::toupper);
        cout << "Message: " << upperMessage << endl;
    }

    void calculateSum(int a, int b) {
        cout << "Sum: " << (a + b) << endl;
    }

    void calculateProduct(int a, int b) {
        cout << "Product: " << (a * b) << endl;
    }

    void reverseString(const string& str) {
        string reversed = string(str.rbegin(), str.rend());
        cout << "Reversed: " << reversed << endl;
    }
};

int main() {
    Utility util;
    util.printMessage("Hello");
    util.calculateSum(5, 10);
    util.calculateProduct(5, 10);
    util.reverseString("world");
    return 0;
}

```
			- 해당 코드는 utility 안에 계산기, 문자열 뒤집기, 문자열 출력등 서로 연관되지 않은 기능이 클래스 내부에 존재한다.


>### 응집도가 높은 코드

```cpp

#include <iostream>
#include <string>
#include <algorithm> // for transform
using namespace std;

class MessageHandler {
public:
    void printMessage(const string& message) {
        string upperMessage = message;
        transform(upperMessage.begin(), upperMessage.end(), upperMessage.begin(), ::toupper);
        cout << "Message: " << upperMessage << endl;
    }
};

class Calculator {
public:
    void calculateSum(int a, int b) {
        cout << "Sum: " << (a + b) << endl;
    }

    void calculateProduct(int a, int b) {
        cout << "Product: " << (a * b) << endl;
    }
};

class StringManipulator {
public:
    void reverseString(const string& str) {
        string reversed = string(str.rbegin(), str.rend());
        cout << "Reversed: " << reversed << endl;
    }
};

int main() {
    MessageHandler messageHandler;
    messageHandler.printMessage("Hello");

    Calculator calculator;
    calculator.calculateSum(5, 10);
    calculator.calculateProduct(5, 10);

    StringManipulator stringManipulator;
    stringManipulator.reverseString("world");

    return 0;
}

```

		- 기능과 연관된 클래스들을 만들어 응집도 높은 클래스들을 만들었다.


### 결합도

결합도는 모듈 또는 클래스 간의 의존성을 나타냅니다.

일반적으로 결합도는 낮을 수록 좋은 코드입니다.

결합도가 높으면 각 모듈이 서로 연결되어

하나의 모듈이 변경되면 다른 모듈들 역시 영향을 받게 됩니다.

### 결합도 실제 예시

>### 결합도 높은 코드

```cpp

#include <iostream>
#include <string>

using namespace std;

// 기존 Engine 클래스
class Engine {
public:
    string state;

    Engine() : state("off") {}

    void start() {
        state = "on";
        cout << "Engine started" << endl;
    }
};

// 새로운 ElectricEngine 클래스 (기존 Engine과는 별도)
class ElectricEngine {
public:
    string state;

    ElectricEngine() : state("off") {}

    void start() {
        state = "on";
        cout << "Electric Engine running silently" << endl;
    }
};

// 기존 Car 클래스 수정
class Car {
public:
    Engine engine; // Car 클래스는 여전히 Engine 클래스에 강하게 의존

    void startCar() {
        if (engine.state == "off") {
            engine.start();
            cout << "Car started" << endl;
        }
    }
};

```

		- car 클래스는 기존 Engine 클래스와 결합도가 높아 Engine의 또 다른 클래스인 ElectricEngine을 사용하기 어렵다.

>### 결합도 낮은 코드

```cpp

#include <iostream>
#include <memory>

using namespace std;

// 공통 인터페이스 정의
class Engine {
public:
    virtual void start() = 0;
    virtual ~Engine() = default;
};

// DieselEngine 구현
class DieselEngine : public Engine {
public:
    void start() {
        cout << "Diesel Engine started" << endl;
    }
};

// 새로운 ElectricEngine 구현
class ElectricEngine : public Engine {
public:
    void start() {
        cout << "Electric Engine started silently" << endl;
    }
};

// Car 클래스는 Engine 인터페이스에만 의존
class Car {
private:
    unique_ptr<Engine> engine;

public:
    Car(unique_ptr<Engine> eng) : engine(move(eng)) {}

    void startCar() {
        engine->start();
        cout << "Car started" << endl;
    }
};

int main() {
    // DieselEngine을 사용하는 경우
    auto dieselEngine = make_unique<DieselEngine>();
    Car dieselCar(move(dieselEngine));
    dieselCar.startCar();

    // ElectricEngine을 사용하는 경우
    auto electricEngine = make_unique<ElectricEngine>();
    Car electricCar(move(electricEngine));
    electricCar.startCar();

    return 0;
}

```

		- 기존 엔진 클래스를 추상 클래스로 바꾸고 해당 자식 클래스를 받는식으로 Car의 엔진 클래스 결합도를 낮추었다.
 

## SOLID 원칙

SOLID 원칙은 선배 개발자들이 검증힌 객체지향 프로그래밍을 위한 설계 원칙입니다.
 
객체지향 설계에서 유지 보수성과 확장성을 높이기 위한 5가지 원칙을 의미합니다.

### 단일 책임 원칙(SRP)

각 클래스는 하나의 책임만 가져야 한다는 원칙입니다.

클래스는 하나의 변경 이유만을 가지고 특정 기능이나 역할을 수행하게 만들어야 합니다.

>### 단일 책임 원칙이 적용되지 않은 코드

```cpp

#include <iostream>
#include <string>

class Student {
public:
    void setName(const std::string& name) {
        this->name = name;
    }

    void displayDetails() {
    }

    void calculateGrade(int score) {
        if (score >= 90) {

        } else if (score >= 80) {

        } else {

        } // 점수를 입력받으면 성적이 출력되는 항목
    }

private:
    std::string name;
};
```
		- Student 항목에 필요 없는 메서드들이 존재.

>### 단일 책임 원칙이 적용된 코드

```cpp

#include <iostream>
#include <string>

// 학생 정보 관리 클래스
class Student {
public:
    void setName(const std::string& name) {
    }

    std::string getName() const {
    }

private:
    std::string name;
};

// 성적 계산 클래스
class GradeCalculator {
public:
    void calculateGrade(int score) {

        }
    }
};

// 출력 클래스
class StudentPrinter {
public:
    void displayDetails(const Student& student)
    {

    }
};

```
			- Student에 필요없는 메서드를 따로 클래스로 구현하였다.

### 개방 폐쇄 원칙(OCP)

코드는 확장에는 열려 있어야 하고, 수정에는 닫혀 있어야 한다는 개념입니다.

기존 코드를 최소한으로 변경하면서 새로운 기능을 추가할 수 있도록 설계해야 합니다.

>### 개방 폐쇄 원칙이 제대로 적용되지 않은 코드

```cpp
class ShapeManager {
public:
    void drawShape(int shapeType) {
        if (shapeType == 1) {
            // 원 그리기
        } else if (shapeType == 2) {
            // 사각형 그리기
        }
    }
};
```
		- ShapeManager에서 모든 도형을 관리하고 있음

>### 개방 폐쇄 원칙이 제대로 적용된 코드

```cpp

class Shape {
public:
    virtual void draw() = 0; // 순수 가상 함수
};

class Circle : public Shape {
public:
    void draw() {
        // 원 그리기
    }
};

class Square : public Shape {
public:
    void draw() {
        // 사각형 그리기
    }
};

class ShapeManager {
public:
    void drawShape(Shape& shape) {
        shape.draw(); // 다형성 활용
    }
};

```
		- 순수 가상 함수와 다형성을 이용해서 개방 폐쇄 원칙에 적합하게 바꾸었다.

### 리스 코프 치환 원칙(LSP)

자식 클래스는 부모 클래스에서 기대되는 행동을 보장해야합니다.

객체지향 프로그래밍에서 다형성을 활용할 때,

부모 클래스를 사용하는 코드가 자식 클래스로 대체되더라도 정상적으로 동작해야 합니다.

즉 자식 클래스의 동작은 부모 클래스의 동작과 일관되게 유지시켜야 합니다.

>### 리스 코프 치환 원칙을 지키지 않은 코드

```cpp

#include <iostream>

class Rectangle {
public:
    virtual void setWidth(int w) { width = w; }
    virtual void setHeight(int h) { height = h; }
    int getWidth() const { return width; }
    int getHeight() const { return height; }
    int getArea() const { return width * height; }

private:
    int width = 0;
    int height = 0;
};

class Square : public Rectangle {
public:
    void setWidth(int w) override {
        Rectangle::setWidth(w);
        Rectangle::setHeight(w); // 정사각형은 너비와 높이가 같아야 함
    }
    void setHeight(int h) override {
        Rectangle::setHeight(h);
        Rectangle::setWidth(h); // 정사각형은 너비와 높이가 같아야 함
    }
};

void testRectangle(Rectangle& rect) {
    rect.setWidth(5);
    rect.setHeight(10);
    std::cout << "Expected area: 50, Actual area: " << rect.getArea() << std::endl;
}

int main() {
    Rectangle rect;
    testRectangle(rect); // Expected area: 50

    Square square;
    testRectangle(square); // Expected area: 50, Actual area: 100 (문제 발생)
    return 0;
}

```
		- 자식인 square에서 부모의 함수가 작동되어 전혀 다른 결과가 나옴

>### 리스 코프 치환 원칙을 지킨 코드

```cpp

#include <iostream>

class Shape {
public:
    virtual int getArea() const = 0; // 넓이를 계산하는 순수 가상 함수
};

class Rectangle : public Shape {
public:
    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
    int getWidth() const { return width; }
    int getHeight() const { return height; }
    int getArea() const override { return width * height; }

private:
    int width = 0;
    int height = 0;
};

class Square : public Shape {
public:
    void setSide(int s) { side = s; }
    int getSide() const { return side; }
    int getArea() const override { return side * side; }

private:
    int side = 0;
};

void testShape(Shape& shape) {
    std::cout << "Area: " << shape.getArea() << std::endl;
}

int main() {
    Rectangle rect;
    rect.setWidth(5);
    rect.setHeight(10);
    testShape(rect); // Area: 50

    Square square;
    square.setSide(7);
    testShape(square); // Area: 49
    return 0;
}

```

		- 기존 부모 클래스의 기본 요소를 새로운 자식 클래스로 분리하고 함수도 가상화 하여 지킴.


### 인터페이스 분리 원칙(ISP)

클라이언트는 자신이 사용하지 않는 메서드에 의존하지 않아야 한다는 원칙입니다.

하나의 거대한 인터페이스보다는 역할별로 세분화된 인터페이스를 만들어,

필요한 기능만 구현하도록 설계해야합니다.

>### 인터페이스 분리 원칙을 지키지 않은 코드

```cpp

class Machnine {
private:

public:
    Machnine() {}

    void print() {
        //세부 기능 구현
    }

    void scan() {
        //세부 기능 구현
    }
};

```

		- print와 scan이 필요없는 machine도 존재한다.

>### 인터페이스 분리 원칙을 지킨 코드

```cpp

class Printer {
public:
    virtual void print() = 0;
};

class Scanner {
public:
    virtual void scan() = 0;
};

class BasicPrinter : public Printer {
public:
    void print() override {
        // 문서 출력
    }
};

class MultiFunctionDevice {//
private:
    Printer* printer;
    Scanner* scanner;

public:
    MultiFunctionDevice(Printer* p, Scanner* s) : printer(p), scanner(s) {}

    void print() {
        if (printer) printer->print();
    }

    void scan() {
        if (scanner) scanner->scan();
    }
};
```
		- 기존 코드를 나누고 기능을 상속받게 만들었다.

### 의존 역전 원칙(DIP)

고수준 모듈은 저 수준 모듈에 직접 의존하는 것이 아니라, 두 모듈 모두 추상화에 의존해야합니다.

즉, 구체적인 구현(저 수준 모듈)에 의존하는 것이 아니라, 인터페이스나 추상 클래스 같은

추상화 계층을 두어 결합도를 낮추는 것이 좋은 설계입니다.

>### 의존 역전 원칙이 적용되지 않은 코드

```cpp
#include<string>

class Keyboard {
public:
    std::string getInput() {
        return "입력 데이터";
    }
};

class Monitor {
public:
    void display(const std::string& data) {
        // 출력
    }
};

class Computer {
    Keyboard keyboard;
    Monitor monitor;

public:
    void operate() {
        std::string input = keyboard.getInput();
        monitor.display(input);
    }
};
```
    - 컴퓨터 하나에 키보드와 모니터가 하나씩만 연결되어 있다.
    - 그렇지만 여러개의 키보드를 사용할때 문제가 생긴다.
    - 그렇다고 컴퓨터를 늘리기는 힘들다.

>### 의존 역전 원칙이 적용된 코드
 ```cpp
#include<string>
class InputDevice {
public:
    virtual std::string getInput() = 0;
};

class OutputDevice {
public:
    virtual void display(const std::string& data) = 0;
};

class Keyboard : public InputDevice {
public:
    std::string getInput() override {
        return "키보드 입력 데이터";
    }
};

class Monitor : public OutputDevice {
public:
    void display(const std::string& data) override {
        // 화면에 출력
    }
};

class Computer {
private:
    InputDevice* inputDevice;
    OutputDevice* outputDevice;

public:
    Computer(InputDevice* input, OutputDevice* output) 
        : inputDevice(input), outputDevice(output) {}

    void operate() {
        std::string data = inputDevice->getInput();
        outputDevice->display(data);
    }
};
```
    - 컴퓨터가 input, output을 쉽게 바꿀 수 있도록 주소값을 이용했다.

#내일배움캠프 #언리얼5기 #객체지향적 설계

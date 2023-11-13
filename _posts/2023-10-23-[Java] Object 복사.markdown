---
layout: single
title: '[Java] Object 복사'
author_profile: true
date: 2023-10-23 00:00:00 +0900
categories: Java
published: true
toc: true
toc_label: 'Table of Contents'
toc_icon: 'list-ul'
toc_sticky: true
sidebar:
  nav: 'docs'
# sidebar:
#   - title: 'Title'
#     image: http://placehold.it/350x250
#     image_alt: 'image'
#     text: 'Some text here.'
#   - title: 'Another Title'
#     text: 'More text here.'
---

객체들을 담은 List에서 stream으로 map 연산을 하다가 객체를 깊은 복사할 수 있는 방법이 필요했다. List의 Stream을 만들고 map 연산으로 새로운 stream을 반환했는데, 원본 List의 객체도 변경되었기 때문이다.

```java
public class Main {
  public static void main(String[] args) {
    List<Book> bookList = Arrays.asList(
        new Book(1L, "book1", "A", "IT", 21600),
        new Book(2L, "book2", "B", "IT", 40500),
        new Book(3L, "book3", "C", "IT", 31500)
    );

    List<Book> discountedBookList = bookList.stream().map(book -> {
        book.setPrice(book.getPrice() * 0.5);
        return book;
    }).toList();

    // stream의 데이터가 객체의 주소값이므로, 원본 list도 변경됨
    for (int i = 0; i < bookList.size(); i++) {
        System.out.println("할인된 책 가격: " + discountedBookList.get(i).getPrice());
        System.out.println("할인 전 책 가격: " + bookList.get(i).getPrice() + "\n");
        System.out.println(discountedBookList.get(i).equals(bookList.get(i)));
    }
  }
}

// Book class
class Book {
  ...
}
```

stream에 담기는 데이터가 객체의 주소값이기 때문에, map()에서 객체를 변경하면 원본 List인 `bookList`도 변경된다.

js에서는 객체를 복사할 때

```javascript
const newObj = { ...old }; // 깊은 복사는 아니지만
```

이렇게 했었는데, java에선 Object를 어떻게 복사하는지 아직도 모르고 있어서 Object 복사하는 방법에 대해 공부했다.

<br>
<br>

# java에서 Object를 어떻게 복사할까?

## 0. Object 얕은 복사 (Shallow copy)

**`clone()` 메서드**

Object.clone() 메서드는 인스턴스 객체를 복제하여 새로운 인스턴스를 생성한다. 하지만, clone()은 참조타입 변수에 대해서는 완전한 복사를 하지 못한다. 즉, clone()은 얕은 복사를 한다.

```java
// Java Object.clone() method
@IntrinsicCandidate
protected native Object clone() throws CloneNotSupportedException;
```

<br>

clone()을 사용하려면, 복사할 클래스가 `Cloneable` interface를 구현해야 하고, clone() 메서드를 overriding해야한다.

복사할 class내에서 clone()을 overriding할 때 접근제어자를 protected에서 public으로 변경해주어, 다른 class에서 clone()을 호출할 수 있게 해야한다.

`Cloneable`을 구현한 class만 복사할 수 있으므로, `Cloneable`을 구현하지 않는 class의 instance를 복사하려고 하면 `CloneNotSupportedException`이 발생한다. 예외처리를 위해 clone()을 overriding할 때 항상 try...catch 안에서 `super.clone()`을 호출해야 한다.

> API를 확인해서 `Cloneable` interface를 구현한 class는 clone()으로 복사할 수 있다.

<br>

위의 예제를 Object.clone()을 사용해서 아래 코드로 바꿨다.

```java
public class Main {
  public static void main(String[] args) {
    ...

    List<Book> discountedBookList = bookList.stream().map(book -> {
      // clone으로 객체 복사
        Book newBook = (Book) book.clone();
        newBook.setPrice(book.getPrice() * 0.5);
        return newBook;
    }).toList();

    ...
  }
}

// Book class
class Book implements Cloneable {
  @Override
    public Object clone() {
        Object obj = null;
        try {
            obj = super.clone();
        } catch (CloneNotSupportedException ignored) {

        }
        return obj;
    }
  ...
}
```

Book 객체의 field 값이 다 primitive 타입이므로 얕은 복사 clone()으로 위의 문제를 해결할 수 있다.

<br>
<br>

## 1. 공변 반환타입 (Covariant return type)

공변 반환타입은 overring할 때 조상 메서드의 return type을 자손 클래스의 타입으로 변경하는 것을 허용하는 것이다.(JDK1.5부터 도입) 공변 반환타입으로 실제로 clone() 메서드를 사용할 때, 번거로운 형변환을 하지 않아도 된다.

위의 예시를 공변 반환타입을 이용해서 아래 코드로 변경하였다.
```java
public class Main {
  public static void main(String[] args) {
    ...

    List<Book> discountedBookList = bookList.stream().map(book -> {
      // clone으로 객체 복사
        Book newBook = book.clone();
        newBook.setPrice(book.getPrice() * 0.5);
        return newBook;
    }).toList();

    ...
  }
}

// Book class
class Book implements Cloneable {
  @Override
    public Book clone() {
        Object obj = null;
        try {
            obj = super.clone();
        } catch (CloneNotSupportedException ignored) {

        }
        return (Book) obj;
    }
  ...
}
```

Array의 경우도 형변환 없이 clone()으로 복사할 수 있는데, 그 이유는 `Cloneable` interface를 구현하고 있고 clone()이 공변 반환타입으로 원본과 같은 타입을 반환하기 때문이다.


<br>
<br>

## 2. Object 깊은 복사 (Deep copy)

위의 예제에서는 Book 객체의 field들이 다 primitive type이어서 clone()을 통한 얕은 복사로도 제대로 복사할 수 있었다. 반면에 객체의 filed에 참조형 변수가 있는 경우에는 

<br>
<br>

# Reference

자바의 정석 - 남궁성

[자바 clone 메서드 재정의 (얕은 복사 & 깊은 복사)](https://inpa.tistory.com/entry/JAVA-%E2%98%95-Object-%ED%81%B4%EB%9E%98%EC%8A%A4-clone-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%96%95%EC%9D%80-%EB%B3%B5%EC%82%AC-%EA%B9%8A%EC%9D%80-%EB%B3%B5%EC%82%AC)

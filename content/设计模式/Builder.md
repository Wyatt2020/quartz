---
created: 2023-10-24 15:00
modified: 2024-07-21 22:53
tags:
  - 设计模式
---


```java
public class Student {
    private int id;
    private String name;
    private int age;
    private String className;

    public Student(int id, String name, int age, String className) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.className = className;
    }

    public static StudentBuilder builder() {
        return new StudentBuilder();
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getClassName() {
        return className;
    }

    public void setClassName(String className) {
        this.className = className;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", className='" + className + '\'' +
                '}';
    }

    public static class StudentBuilder {
        private int id;
        private String name;
        private int age;
        private String className;

        StudentBuilder() {
        }

        public StudentBuilder id(int id) {
            this.id = id;
            return this;
        }

        public StudentBuilder name(String name) {
            this.name = name;
            return this;
        }

        public StudentBuilder age(int age) {
            this.age = age;
            return this;
        }

        public StudentBuilder className(String classNme) {
            this.className = className;
            return this;
        }

        public Student build() {
            return new Student(this.id, this.name, this.age, this.className);
        }
    }
}
```

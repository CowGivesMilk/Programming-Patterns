```cpp
class Singleton {
 private:
  Singleton() {}

 public:
  static Singleton& instance() {
    static Singleton* instance = new Singleton();
    return *instance;
  }
};
```
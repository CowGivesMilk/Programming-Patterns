```cpp
class Singleton {
 private:
  static Singleton* instance_;
  Singleton() {}

 public:
  static Singleton& instance() {
    if (instance_ == nullptr) instance_ = new Singleton();
    return instance_;
  }
};
```
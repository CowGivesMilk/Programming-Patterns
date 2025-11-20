```cpp
// ----------------- Observer Interface -----------------
class Observer {
 public:
  virtual ~Observer() = default;
  virtual void onNotification(const std::string& notification) = 0;
};
// ----------------- Subject interface -----------------
class Subject {
 public:
  virtual ~Subject() = default;
  virtual void addObserver(Observer* obs) = 0;
  virtual void removeObserver(Observer* obs) = 0;
  virtual void notify(const std::string& notification) = 0;
};
// ------------------ Concrete Subject -------------------
class ConcreteSubject : public Subject {
  std::vector<Observer*> observers;

 public:
  void addObserver(Observer* obs) override { observers.push_back(obs); }

  void removeObserver(Observer* obs) override {
    observers.erase(std::remove(observers.begin(), observers.end(), obs),
                    observers.end());
  }

  void notify(const std::string& notification) override {
    for (auto* obs : observers) {
      obs->onNotification(notification);  // <--- key point: we call them
    }
  }
};
// ------------------ Concrete Observer -------------------
class ConcreteObserver : public Observer {
  std::string name;

 public:
  explicit ConcreteObserver(std::string n) : name(n) {}

  void onNotification(const std::string& notification) override {
    std::println("{} recieved {}", name, notification);
  }
};

int main() {
  ConcreteSubject subject;

  ConcreteObserver a("Observer A");
  ConcreteObserver b("Observer B");

  subject.addObserver(&a);
  subject.addObserver(&b);

  subject.notify("Event 1 happened!");
}
```
[^1]
#### Output
```txt
Observer A received: Event 1 happened!
Observer B received: Event 1 happened!
```

[^1]: Line 24 of code: erase-remove idiom. Remove actually moves the unwanted items to last and returns the iterator to new logical end and erase actually removes them.
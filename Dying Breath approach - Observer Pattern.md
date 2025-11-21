```cpp
class Subject;

class Observer {
 public:
  std::vector<Subject*> subjects_;

  virtual ~Observer() {
    // Just tell subjects to remove me.
    for (Subject* sub : subjects_) {
    // safe: subject removes me from its list
      sub->removeObserver(this);
    }
    subjects_.clear();
  }

  void addSubject(Subject* subject) {
	  subjects_.push_back(subject);
	}
  void removeSubject(Subject* subject) {
    subjects_.erase(
	    std::remove(subjects_.begin(), subjects_.end(), subject),
        subjects_.end());
  }

  virtual void onNotification(const std::string& msg) = 0;
};
class Subject {
 public:
  std::vector<Observer*> observers_;

  virtual ~Subject() {
    // Tell observers to forget me.
    for (Observer* obs : observers_) {
    // safe: observer removes from its list
      obs->removeSubject(this);
    }
    observers_.clear();
  }

  void addObserver(Observer* obs) {
    observers_.push_back(obs);
    obs->addSubject(this);
  }

  void removeObserver(Observer* obs) {
    observers_.erase(
	    std::remove(observers_.begin(), observers_.end(), obs),
        observers_.end());
    obs->removeSubject(this);
  }

  void notify(const std::string& msg) {
    for (Observer* obs : observers_) {
      obs->onNotification(msg);
    }
  }
};
```
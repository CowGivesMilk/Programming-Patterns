Many of the singleton classes are “managers” — those nebulous classes that exist just to babysit other objects. Codebases where it seems like every class has a manager: Monster, MonsterManager, Particle, ParticleManager, Sound, SoundManager, ManagerManager.

```cpp
class Bullet {
 public:
  int getX() const { return x_; }
  int getY() const { return y_; }
  void setX(int x) { x_ = x; }
  void setY(int y) { y_ = y; }

 private:
  int x_, y_;
};
class BulletManager {
 public:
  Bullet* create(int x, int y) {
    Bullet* bullet = new Bullet();
    bullet->setX(x);
    bullet->setY(y);
  }
  return bullet;
  bool isOnScreen(Bullet& bullet) {
    return bullet.getX() >= 0 && bullet.getX() < SCREEN_WIDTH &&
           bullet.getY() >= 0 && bullet.getY() < SCREEN_HEIGHT;
  }
  void move(Bullet& bullet) { bullet.setX(bullet.getX() + 5); }
};
```

**What is the solution ?**
The answer here is zero, actually. Here’s how we solve the “singleton” problem for our manager class:
```cpp
class Bullet {
 public:
  Bullet(int x, int y) : x_(x), y_(y) {}
  bool isOnScreen() {
    return x_ >= 0 && x_ < SCREEN_WIDTH && y_ >= 0 && y_ < SCREEN_HEIGHT;
  }
  void move() { x_ += 5; }

 private:
  int x_, y_;
};
```

There we go. No manager, no problem. Poorly designed singletons are often “helpers” that add functionality to another class. If you can, just move all of that behavior into the class it helps. After all, OOP is about letting objects take care of themselves.

Outside of managers, though, there are other problems where we’d reach to Singleton for a solution. For each of those problems, there are some alternative solutions to consider.
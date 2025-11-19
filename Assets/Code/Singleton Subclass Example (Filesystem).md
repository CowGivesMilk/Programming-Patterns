```cpp
class FileSystem {
 public:
  virtual ~FileSystem() {}
  virtual char* readFile(char* path) = 0;
  virtual void writeFile(char* path, char* contents) = 0;
};
// Then we define derived classes for a couple of platforms:
class PS3FileSystem : public FileSystem {
 public:
  virtual char* readFile(char* path) {
    // Use Sony file IO API...
  }
  virtual void writeFile(char* path, char* contents) {
    // Use sony file IO API...
  }
};
class WiiFileSystem : public FileSystem {
 public:
  virtual char* readFile(char* path) {
    // Use Nintendo file IO API...
  }
  virtual void writeFile(char* path, char* contents) {
    // Use Nintendo file IO API...
  }
};
// Next, we turn FileSystem into a singleton:
class FileSystem {
 public:
  static FileSystem& instance();
  virtual ~FileSystem() {}
  virtual char* readFile(char* path) = 0;
  virtual void writeFile(char* path, char* contents) = 0;

 protected:
  FileSystem() {};
};
// The clever part is how the instance is created:
FileSystem& FileSystem::instance() {
#if PLATFORM == PLAYSTATION3
  static FileSystem* instance = new PS3FileSystem();
#elif PLATFORM == WII
  static FileSystem* instance = new WiiFileSystem();
#endif
  return *instance;
}
```
Our entire codebase can access the file system using FileSystem::instance() without being coupled to any platform-specific code. That coupling is instead encapsulated within the implementation file for the FileSystem class itself.
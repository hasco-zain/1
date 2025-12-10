#include <iostream>

class DynamicArray {
private:
    int* data;
    size_t size;
    
    void cleanup() { delete[] data; data = nullptr; size = 0; }
    void copyFrom(const DynamicArray& other) {
        size = other.size;
        data = new int[size];
        for(size_t i = 0; i < size; ++i) data[i] = other.data[i];
    }

public:
    DynamicArray() : data(nullptr), size(0) {}
    DynamicArray(size_t s) : size(s), data(new int[s]()) {}
    DynamicArray(std::initializer_list<int> init) : size(init.size()), data(new int[size]) {
        size_t i = 0; for(auto val : init) data[i++] = val;
    }
    
    // Move constructor (steals pointer)
    DynamicArray(DynamicArray&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr; other.size = 0;
        std::cout << "Move ctor ";
    }
    
    ~DynamicArray() { cleanup(); }
    
    // Move assignment (steals pointer)
    DynamicArray& operator=(DynamicArray&& other) noexcept {
        if(this != &other) {
            cleanup();
            data = other.data; size = other.size;
            other.data = nullptr; other.size = 0;
            std::cout << "Move assign ";
        }
        return *this;
    }
    
    // Required operators
    int& operator[](size_t idx) { return data[idx]; }
    explicit operator bool() const { return size > 0; }
    bool operator==(const DynamicArray& other) const {
        if(size != other.size) return false;
        for(size_t i = 0; i < size; ++i) if(data[i] != other.data[i]) return false;
        return true;
    }
    DynamicArray operator+(const DynamicArray& other) const {
        DynamicArray result(size + other.size);
        for(size_t i = 0; i < size; ++i) result.data[i] = data[i];
        for(size_t i = 0; i < other.size; ++i) result.data[size + i] = other.data[i];
        return result;
    }
    
    // Iterator-like access
    int* begin() { return data; }
    int* end() { return data + size; }
    void print() const {
        std::cout << "[";
        for(size_t i = 0; i < size; ++i) std::cout << (i ? ", " : "") << data[i];
        std::cout << "]\n";
    }
};

class SequenceGen {
    int current = 0, step = 1;
public:
    SequenceGen(int start = 0, int stepSize = 1) : current(start), step(stepSize) {}
    int operator()() { int val = current; current += step; return val; }
};

template<typename Iterator, typename Generator>
void FillArray(Iterator begin, Iterator end, Generator gen) {
    for(Iterator it = begin; it != end; ++it) *it = gen();
}

template<typename T>
T moveToNewObject(T&& source) {
    std::cout << "Template move ";
    return std::move(source);
}

int main() {
    // Create arrays
    DynamicArray arr1 = {1, 2, 3};
    DynamicArray arr2(2);
    
    // Fill using SequenceGen
    FillArray(arr2.begin(), arr2.end(), SequenceGen(10, 5));
    
    // Demonstrate move
    DynamicArray arr3 = moveToNewObject(std::move(arr1));
    std::cout << "\narr3 after move: "; arr3.print();
    
    // Demonstrate concatenation
    DynamicArray arr4 = arr3 + arr2;
    std::cout << "Concatenated: "; arr4.print();
    
    // Demonstrate operators
    std::cout << "bool(arr4): " << static_cast<bool>(arr4) << "\n";
    std::cout << "arr4[1]: " << arr4[1] << "\n";
    
    return 0;
}
int main() {
 
    DynamicArray arr1 = {1, 2, 3};
    DynamicArray arr2(2);
    
   
    FillArray(arr2.begin(), arr2.end(), SequenceGen(10, 5));
    
 
    DynamicArray arr3 = moveToNewObject(std::move(arr1));
    arr3.print();
    

    DynamicArray arr4 = arr3 + arr2;
    arr4.print();
    

    std::cout << "bool(arr4): " << 
    std::cout << "arr4[1]: " << arr4[1] << "\n";
    
    return 0;
}

# boost_library

## Lexical_cast

```cpp
#include <iostream>
#include <boost\Lexical_cast.hpp>
using namespace std;
using boost::lexical_cast;
using boost::bad_lexical_cast;

int main() {
	int s = 23;
	string str  = lexical_cast<string>(s);
	str = "Message: " + lexical_cast<string>('A') + "=" + lexical_cast<string>(34.5);
	arrary<char, 64> msg = lexical_cast<<arrary<char, 64>(23);
	s = lexical_cast<int>("2345");
	try {
		s = lexical_cast<int<("345.89");
	} catch(bad_lexical_cast &e) {
		cout << "Exception caught: " << e.what() << endl;
	}
	s = lexical_cast<int>("35345ur"); // bad lexcal_cast
	s = lexcal_cast<iny>("3456", 4) // good
}
```

## variant

```cpp
#include <iostream>
#include <boost/variant.hpp>

using namespace std;

int main() {
    //union
    union {int i; float f;} u;
    u.i = 34;
    // u.i is overwritten
    u.f = 2.3;
    
    boost::variant<int, string> u1, u2;
    u1 = 2;
    u2 = "hello";
    cout << u1 << "" << u2 << endl;
    
    // u1 = u2 *2
    u1 = boost::get<int>(u1)*2;
    // string st = boost::get<string>(u1); // throw an exception
    u1 = "good"; // u1 becomes a string
    u1 = 32; // u1 becoumes an int again
    
    // u3 default is the first template class-int
    boost::variant<int, string> u3; // u3 is 0
    
    void Double(boost::variant<int, string> v);
    
    // using vistor
    class DoubleVisitor : public boost::static_visitor<> {
    public:
        void operator() (int& i) const {
            i += i;
        }
        void operator() (string& str) const {
            str += str;
        }
    };
    u1 = 2;
    boost::apply_visitor(DoubleVisitor(), u1); // u1 becomes 4
    u2 = "hello";
	boost:apply_visitor(DoubleVisitor(), u2); //u2 becomes hellohello
    
    std::vector<boost::variant<int, string>> v;
    v.push_back("good");
    v.push_back("23");
    v.push_back("bad");
    
    DoubleVisitor f;
    for (auto x : v) {
        boost::apply_visitor(f, x);
        cout << x << endl;
    }
}

```


## boost any

can store any type of data

```cpp
#include <iostream>
#include <boost/any.cpp>

using namespace std;

int main () {
	boost::any x, y, z;
	x = string("hello");
	x = 2.3;
	y = 'z';
	z = std::vector<int>(); // use dynamic storage

	// returns the copy data of y: 'z'
	cout << boost::any_cast<char>(y) << endl;

	// returns the copy data of x: 2.3;
	cout << boost::any_cast<double>(x) <<endl;

	cout << boost::any_cast<int>(x) << endl; // exception

	cout << boost::any_cast<float>(x) << endl; //exception

	if(x.empty()) {
		cout << "x is empty" << endl;
	}

	if (x.type() == typeid(char)) {
		cout << " x is char" << endl;
	}

	// any cast will return copy of the element
	boost::any_cast<vector<int>>(z).push_back(23);
	int i = boost::any_cast<vector<int>>(z).back(); // crashed

	int i;
	boost::any pi = &i;
	int *pInt = boost::any_cast<int*>(p); // return a pointer

	std::vector<boost:any> m;
	m.push_back(23);
	m.push_back('a');
	m.push_back(p);
	m.push_back(boost::any());

	struct property {
		string name;
		boost::any value;
	};

	std::vector<property> v;
}
```

### Optional

```cpp
#include <iostream>
#include <deque>
#include <boost/optional.hpp>

using namespace std;

std::deque<char> queue;

boost::optional<char> get_anyc_data() {
    if(!queue.empty()){
        return boost::optional<char>(queue.back());
    }
    return boost::optional<char>();
}
int main() {
    // op is not initialized no char is constructed
    boost::optional<char> op;
    op = 'A';
    op = get_anyc_data();
    if(!op){
        cout << "No data is available" << endl;
    } else {
        cout << "op contains " << op.get() << endl;
        cout << "op contains " << *op << endl;
    }
    
    op.reset(); // reset op to uninitialized state
    cout << op.get_value_or('z') << endl; // return op value or default value by 'z'
    
    // optional can store any kind of data
    struct A {string name; int value;};
    A a{"bob", 19};
    boost::optional<A> a1;
    boost::optional<A> a2(a);
    cout << a2->name << " " << a2->value << endl;
    
    //Pointer
    boost::optional<A*> a3(&a);
    (*a3)->name = "bob";
    
    //Reference
    boost::optional<A&> a4(a);
    a4->name = "dad";
    
    // relational operator
    boost::optional<int> i1(9);
    boost::optional<int> i2(10);
    
    if(i1 < i2) {
        // if i2 is not initialized, i2 is smaller
        cout << "i1 is smaller" << endl;
    }
    
}
```

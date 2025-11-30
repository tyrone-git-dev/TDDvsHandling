# TDDvsHandling
TDD in OOP seems redundant, defined error handling and Business Logic test cases make more sense.

When completing TDD in OOP, you end with the converse argument, does the class work? Classes always work when designed properly. 
Allowing an environment that makes arguments and code redundant is a debt issue and I would like to tackle this opinion with an example using a personalized BST. 

When creating the code base, the base, you generally start with a test case in a test environment, the proto, then implement the code. Here, we want to create a PersonalTree class.



```
class PersonalTreeTest {
	@Test
	void testPersonalTreeClass() {
		fail("Not yet implemented");
	}
}
```
With the test created, we can go into the base and properly create a PersonalTree class.

```
public class PersonalTree { }
```

The test case environment requires at minimum these elements:
- testClass(), fail()
- testValidClass
- testValidEntity // BST

Let us continue with creating the ValidClass test. In this case, we create a Tree and simply assert it is true, created successfully.

```
void testIsValidPersonalTree() {
	// create PersonalTree class
	PersonalTree tree = new PersonalTree();
	assertEquals(tree, new PersonalTree());		
}
```

The test above enables us to do the following:
- Import a Node library into our PersonalTree class
- Further express the PersonalTree class by setting default constructions that use the Node library

Given this is not a personally created Node class, of which has been tested time and time again, I excluded the implementation and created the test cases for testing the 
Node, especially since this would be tested in another class, formally.

```
import com.example.Node;

public class PersonalTree {
	Node root;
	
	public PersonalTree() {
		root = null;
	}
}
```

The next step is to create the ValidEntity test. In this case, we would use the tested Node class methods and make sure that the Tree we are creating is 
a valid tree entity, specifically a BST.

```
void testIsValidBST(Node node, int min, int max) {
  if(node == null) assertTrue(true);
  
  if (node.getKey() < min || node.getKey() > max) assertTrue(false);
  
  
  while (node != null) {
    testIsValidBST(node.getLeft(), min, node.getKey() - 1);
    testIsValidBST(node.getRight(), node.getKey() + 1, max);
  }
  
  
  assertTrue(true);
}
```

Notice that after creating this test we did not have to add anything to the Tree class. This is a great example of what I like to call business logic 
testing. The business requires their own Tree model, and this is a great business logic test case to validate that the model the business requires is accurate.


The last step would be to create the function tests and implement the functions into the class. This is the stem of the argument, will return later.

```
void testFindKey(Node root, int key) {
	if(root == null)
		assertTrue(false);
	if (root.getKey() == key)
		assertTrue(true);
	if (root.getKey() < key)
	{ // testing recursion
		while (root != null) {
			testFindKey(root.getRight(), key);
		}
	}
	
	while (root != null) {
		testFindKey(root.getLeft(), key);
	}
	
	assertTrue(false);
}
```

And the implement:

```
public class PersonalTreeUtils {
	public boolean findKey(Node root, int key) {
		if(root == null)
			return false;
		if (root.getKey() == key)
			return true;
		
		if (root.getKey() < key)
		{
			while (root != null) {
				return findKey(root.getRight(), key);
			}
		}
		
		while (root != null) {
			return findKey(root.getLeft(), key);
		}
		
		return false;
	}
}
```

And here is the problem with TDD. In practice, we have done two foundational actions:
- We have tested literally
- We have tested business logic.

In literal test cases, we test methods and components such as fail casese, common-sense attacks, and I/O errors. When put together, are we asking if the 
class works as intended, or asking if classes work as intended. When using TDD in OOP, we are essentially testing with extreme practices, but a lot of 
the code is redundant. Is it necessary to replicate the development environment into a test environment, just to replicate it in the QA environment, then 
duplicate it in the actual environment. You end up managaing 4 different branches, meaning documentation for Gitlab pulls, pushes, and merges, adding an 
additional layer of complexity and, in my opinion, adds to creeping tech debt, for managing a single style of data structure. 

Lets check another implementation of PersonalTree and PersonalTreeUtils using error handling:

```
public class PersonalTreeII {
	Node root;
	
	public PersonalTreeII() throws Exception {
		if (root != null) throw new Exception("PersonalTreeII default constructor error: root can not be initialized
							in this class.");
		root = null;
	}
	
	public Node getNode() throws Exception {
		if (root != null) throw new Exception("PersonalTreeII getNode() error: root can not have a value.");
		return root;
	}
}
```

```
public class PersonalTreeUtilsII {
	public boolean findKey(Node root, int key) throws Exception {
		if(root == null)
			throw new Exception("PersonalTreeUtilsII findKey() error: root can not be null.");
		if (root.getKey() == key)
			return true;
		
		if (root.getKey() < key)
		{
			while (root != null) {
				return findKey(root.getRight(), key);
			}
		}
		
		while (root != null) {
			return findKey(root.getLeft(), key);
		}
		
		return false;
	}
}
```

Class, function, error description. But what about the business logic?

```
class PersonalTreeTestII {

	@Test
	void testPersonalTreeIIClass() {
		fail("Not yet implemented");
	}
	
	// void testIsValidNode() { }
	
	void testIsValidBST(Node node, int min, int max) {
		if(node == null) assertTrue(true);
		
		if (node.getKey() < min || node.getKey() > max) assertTrue(false);
		
		
		while (node != null) {
			testIsValidBST(node.getLeft(), min, node.getKey() - 1);
			testIsValidBST(node.getRight(), node.getKey() + 1, max); // Crypto
		}
		
		
		assertTrue(true);
	}
		
	
	// test that PersonalTree can find a key
	
	// test that the PersonalTree can find and return a key
	
	// test that PersonalTree can insert a key
	
	// ..

}
```

Error handling and Business logic test cases/suites. 
- Uses descriptive exception handling vs test cases.
- Still has fail-safe cases
- Has business logic that is tested as a whole unit, or with associative test functions that prove the business logic. Mocks and stubs for server-request tests.
- Dramatic cut-down on redundancy tests


TDD sounds more appropriate in imperative-based design. Everything is a function. How do the functions order, how do the functions interact. These sound like business logic executions, of which you can test in both OOP and imperative designs. 

If everything is a class, that means that business logic and interactions should be tested, not logic. When everything is a class, what logic need to be tested? In imperative you are more likely to come across a function embedded within a function, both of which need to be tested separately as VALID CODE as well as ENTITIES. Class design should not contain run-time errors, of which needs to be tested in imperative designs. If a server happens to be a class, you are testing behaviors, not logic.

When it comes to OOP, I believe that error handling, handling, is much more useful. Error handling lets you test, handle, what has been implemented into the class. Compile-time checks do not need to be tested extremely as classes should not provide compile-time errors for a class is completed when initilaized empty. Functions should not be initialized empty as the whole test case can be completed before initializing an implementation. In OOP, you are virtually required to create the class first before testing whether or not it is valid, for a class is an established entity, not an anonymous function. Initialized empty. 

TDD for imperative designs is a sanity/syntax check for declared/declaring behaviors. TDD for OOP designs is a sanity/syntax check for class behaviors?

Are we over-abusing the proxy design pattern in OOP ??

Remote class vs Remote imperative

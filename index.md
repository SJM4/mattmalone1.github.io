## Welcome to My ePortfolio

### Professional Self-Assessment
  This ePortfolio consists of three items that are toy programs from my computer science undergrad program in the areas of software development and design, in algorithms and data structures, and in database technology. I will be adding to it as I do some more in depth programs. So please check back at regular intervals. In the first artifact I translated from Java to C++ a program which reads tab delimited file with data containing the usernames, MD5 passwords, regular password, and type of account that it is. The program parses that data into its basic pieces at the tab points and requests and reads the user input of username and password. The program converts the password into an MD5 hash and compares it to the MD5 password and username read into memory by the program. It then opens correct type of file for the account type like a login with specific access would do. The second artifact is a for algorithms and data structures shows a binary search tree reading in a list of bid data and then giving the option of displaying all of the bids, finding a specific item from the bids, and removing a bid from the binary search tree data structure. It also implements a vector data structure and uses that to load the same bid data in a vector structure. This vector structure allows one to display all the bids then it gives the options to perform selection sort or quick sort. The third artifact uses python and the pymango driver to manipulate a mangoDB. It  uses a framework called bottle which was supplied by the university to connect with the database stocks and it creates, reads, updates, deletes, pulls the fifty day simple averages in a range, pulls all items from a specific industry, and aggregates an entire sector of the market data provided. 

### Artifact One - Software Design and Engineering

  This artifact starts as a Java program which reads username and password data from a file and compares it to username and password data entered by the user. The password is an MD5 cryptographic hash from a Java library. This artifact was originally created in August 2018 for IT-145 Foundation in Application Development the core Java development class.
  I selected this artifact because it was a reasonable length application which would showcase reading from files, parsing the information from the files, storing the correct login information, using a library for the MD5 password, and retrieving and printing to the screen the data in the authorized account type. The artifact was completely rewritten in C++11 to show fluency in both Java and C++.
  I met the course objectives in Part I software Design/Engineering by reviewing each structure in Java and converting the logic over to C++. I do not have any updates to my outcome plan.
  As I modified this artifact, I learned that C++ can be a little temperamental when dealing with stream inputs from a file. It doesn’t have some of the built-in controls that a newer language like Java has for parsing out each item in the tab delimited file. I had to read a line at a time from the file then generate a stream split and store to a vector for each field. I was able to access the individual entries in the file and save those that matched the username and password file. There is also not a built in digest function for MD5 creation. In order to test the password which was in MD5 in the file I needed to find and integrate an MD5 conversion program. I found one that was open source and was able to test the output to ensure it was accurate. The program is fully functional because I tested the input and output at each stage in every object and object function.

```
/*
 * role.h
 *
 *  Created on: Jul 22, 2020
 *      Author: Matthew Malone
 */

#ifndef ROLE_H_
#define ROLE_H_

#include <string>

class Role {
private:
	std::string User;

public:
	Role(std::string User);
	void Display();
};
#endif /* ROLE_H_ */

/*
 * Verification.h
 *
 *  Created on: Jul 22, 2020
 *      Author: Matthew Malone
 */

#ifndef VERIFICATION_H_
#define VERIFICATION_H_

#include <string>

class Verification {
	private:
		bool authenticate = false;
		void Auth();

    // This constructor gathers the information needed to create a verification
    // object. It call the private methods Auth() and MD5() to accomplish this.
	public:
		Verification();
		bool getAuth();
		std::string getUser();
};
#endif /* VERIFICATION_H_ */

/*
 * Author: Matthew Malone   matthew.malone1@snhu.edu
 * Date: July 21, 2020
 * Program: Authentication.cpp
 * Description: This program has two classes one which verifies the username
 * and password and one which displays the role the user is trying to access.
 *
 */

#include <cstdio>
#include <cstdlib>
#include <iostream>
#include "verification.h"
#include "role.h"

using namespace std;

int main() {

    // Authenticate the username and password
    Verification Ver;

    // If authentication succeeds print the information in the role object.
    if (Ver.getAuth()) {

        Role role1(Ver.getUser()); // set the path

        try{
            role1.Display();  // Display the role file.
        }
        catch (exception e){
            cout << "IOException from the role display function." << endl;
            exit(3);
        }
    }



}

/*
 * Author: Matthew Malone   matthew.malone1@snhu.edu
 * Date: July 21, 2020
 * File: role.cpp
 * Description: This file opens first sets the path for the role description
 * file. Then it will open and print that file when the Display method is
 * called.
 */

#include <string>
#include <iostream>
#include <fstream>

using namespace std;

class Role {
	private:
		string path;
		string User;

	public:
		Role(string);
		void Display();
		string getPath();
};


Role::Role(string User){
    // setup the path object
    path = User + ".txt";
}


string Role::getPath(){
	return path;
}

void Role::Display() {
	string line;


	ifstream instring(getPath(), ifstream::in);


	// Check that the stream is valid
	if (!instring.good()) {
		cout << "Input stream to file " << path
			<< " is not valid" << endl;
		}

		// If we get here the file stream is valid

		// Print the accessed file
		while(getline(instring, line)) {
			cout << line << endl;
        }


        instring.close();
    }


/*
 * Author: Matthew Malone   matthew.malone1@snhu.edu
 * Date: July 21, 2020
 * File: Verification.cpp
 * Description: This file creates an object which will verify the authenticity
 * of a username and password combination against a credentials file using
 * a MD5 hash.
 */

#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <fstream>
#include <vector>
#include <iterator>
#include <sstream>
#include "MD5.h"

using namespace std;

class Verification {
	private:
		string username = " ";
		string MD5password;
		string type = "";
		bool authenticate = false;
		void Auth();
		void split(const std::string &s, char delim, std::vector<std::string> &elems);

    // This constructor gathers the information needed to create a verification
    // object. It call the private methods Auth() and MD5() to accomplish this.
	public:
		Verification();
		bool getAuth();
		string getUser();
};

	Verification::Verification() {
		int count = 0;
        string password = "";

        // Allow the users to exit if quit is typed.
        while(username.compare("quit")) {

            // get username
            cout << "To quit type 'quit' in the username field." << endl;;
            cout << "Enter Username: ";
            cin >> username;

            // facilitate quitting if quit is typed.
            if (!username.compare("quit")) {
                continue;
            }

            // get password
            cout << "Enter Password: ";
            cin.ignore();
            getline(cin, password, '\n');

            // Use a try catch block to handle any MD5 exception.
            try {
            	MD5password = md5(password); //Call the provided MD5 function
            }
            catch (exception e){
                cerr <<"MD5 is throwing an exception." << endl;
                exit(1);
            }

            // Auth performs username and password comparisons
            try {
            	Auth();
            } catch (exception e){
                cerr << "Auth is throwing an exception." << endl;
                exit(2);
            }

            // If the authentication object now reads true return to main.
            if (authenticate) {
                break;
            }

            ++count;
            // break if the login attempts are equal to 3.
            if (count >= 3){
                cout << "Authentication Failed" << endl;
                break;
            }

        }
    }

	// taken from http://stackoverflow.com/a/236803/248823
	void Verification::split(const std::string &s, char delim, std::vector<std::string> &elems) {
	    std::stringstream ss;
	    ss.str(s);
	    std::string item;
	    while (std::getline(ss, item, delim)) {
	        elems.push_back(item);
	    }
	}


    // This method matches the username and password determining if the
    // authentication is true or not.
    void Verification::Auth() {
        string password = "";
        string user = "";
        string line;
        string passTemp, userTemp;

        //System.out.println(System.getProperty("user.dir"));
        // The directory structure automatically defaulted to the top level
        // of the Authentication folder. By opting to keep all the files
        // together I needed to extend the file path.
        ifstream inFS("credentials.txt", ifstream::in);

        // Check that the stream is valid
        if (!inFS.good()) {
            std::cout << "Input stream to file " << "credentials.txt"
                << " is not valid" << endl;
        }

        // If we get here the file stream is valid

        while(getline(inFS, line))
            // Get the items that matter in the password file.
        	{
        	    vector<string> row_values;

        	    split(line, '\t', row_values);

        	    userTemp = row_values[0];
        	    passTemp = row_values[1];

        	    if((!userTemp.compare(username)) && (!passTemp.compare(MD5password))){

        	    	user = row_values[0];
        	    	password = row_values[1];
        	    	type = row_values[3];

        	    }
        	}
            // Compare given password and username to the file password and
            // username.
            if ((!password.compare(MD5password)) && (!user.compare(username))) {
            	authenticate = true;
            }
       inFS.close();
    }

    bool Verification::getAuth(){
        return authenticate;
    }
    string Verification::getUser() {
    	return type;

    }

```


### Artifact Two - Algorithm and Data Structures

  This artifact was the final lab for CS-260: Data Structures and Algorithms showing only binary tree functions: Load, Display, Find, and Remove were previously implemented by me in the lab. My work is only in BinarySearchTree.cpp the other files that read in the data CSVparser.cpp and CSVparser.hpp were provided in this assignment. It was originally created February of 2019.
  I am including this artifact because it starts by showing the implementation of a binary search tree and the related manipulations of said search tree. The artifact was improved by adding the vector data structure in addition to the binary tree this data structure allowed for the implementation of quick sort and selection sort. The artifact showcases my skills with algorithms and data structures by demonstrating the searching and sorting data structures and algorithms. Measuring the speeds on the different data structures when used with the different data sets is a useful way of demonstrating the efficiencies or lack there of for each algorithm.
  I mostly met the enhancements I planned in module one though I did not implement some of the more exotic searching algorithms. This narrowing of the variety of searching algorithms is the only change to the outcomes-coverage plans.
  I refreshed my memory as to the function of Binary Search trees and the implementation of vectors to hold the same type of information. As I added the vector representation, I learned a lot about quick sort and selection sort. Challenges came in the form of ensuring the algorithms met the specification. I learned a lot from the partition function that needed to be implemented as a part of the quick sort algorithm.

```


//============================================================================
// Name        : BinarySearchTree.cpp
// Author      : Matthew Malone
// Version     : 1.0
// Copyright   : Copyright Â© 2017
// Description : Binary Search tree with load bids, display bids, find bids,
//    and remove bids. Adding to it a vector representation of the bids
//    and showing quick sort and selection sort.
//============================================================================

#include <iostream>
#include <cstdlib>
#include <climits>
#include <algorithm>
#include <string>
#include <time.h>

#include "CSVparser.hpp"

using namespace std;

//============================================================================
// Global definitions visible to all methods and classes
//============================================================================

// forward declarations
double strToDouble(string str, char ch);

// define a structure to hold bid information
struct Bid {
    string bidId; // unique identifier
    string title;
    string fund;
    double amount;
    Bid() {
        amount = 0.0;
    }
};

// FIXME (1): Internal structure for tree node
struct Node {
	Bid bid;
	Node* left;
	Node* right;

	//default constructor
	Node() {
		left = nullptr;
		right = nullptr;
	}

	// initialize with a given bid
	Node(Bid abid) : Node() {
		this->bid = abid;
	}

};

//============================================================================
// Binary Search Tree class definition
//============================================================================

/**
 * Define a class containing data members and methods to
 * implement a binary search tree
 */
class BinarySearchTree {

private:
    Node* root;

    void addNode(Node* node, Bid bid);
    void inOrder(Node* node);
    Node* removeNode(Node* node, string bidId);

public:
    BinarySearchTree();
    virtual ~BinarySearchTree();
    void InOrder();
    void Insert(Bid bid);
    void Remove(string bidId);
    Bid Search(string bidId);
};

/**
 * Default constructor
 */
BinarySearchTree::BinarySearchTree() {
    // initialize housekeeping variables
	root = nullptr;
}

/**
 * Destructor
 */
BinarySearchTree::~BinarySearchTree() {
    // recurse from root deleting every node
}

/**
 * Traverse the tree in order
 */
void BinarySearchTree::InOrder() {
	this->inOrder(root);
}
/**
 * Insert a bid
 */
void BinarySearchTree::Insert(Bid bid) {
    // FIXME (2a) Implement inserting a bid into the tree
	if (root == nullptr) {
		root = new Node(bid);
	} else {
		this->addNode(root, bid);
	}
}

/**
 * Remove a bid
 */
void BinarySearchTree::Remove(string bidId) {
    // FIXME (4a) Implement removing a bid from the tree
	this->removeNode(root, bidId);
}

/**
 * Search for a bid
 */
Bid BinarySearchTree::Search(string bidId) {
    // FIXME (3) Implement searching the tree for a bid

	// start searching from the root
	Node* current = root;

	// keep looping downwards until the bottom is reached or bid is found
	while (current != nullptr) {
		// if current node matches, return it
		if (current->bid.bidId.compare(bidId) == 0) {
			return current->bid;
		}
		// if current bid is smaller than current than traverse left
		if (bidId.compare(current->bid.bidId) < 0) {
			current = current->left;
		} else {
			current = current->right;
		}
	}

	Bid bid;
    return bid;
}

/**
 * Add a bid to some node (recursive)
 *
 * @param node Current node in tree
 * @param bid Bid to be added
 */
void BinarySearchTree::addNode(Node* node, Bid bid) {
    // FIXME (2b) Implement inserting a bid into the tree

	// if node larger than the bid, add to left subtree
	if (node->bid.bidId.compare(bid.bidId) > 0) {
		if (node->left == nullptr) {
			node->left = new Node(bid);
		} else {
			this->addNode(node->left, bid);
		}
		//add to right subtree
	} else {
		if (node->right == nullptr) {
			node->right = new Node(bid);
		} else {
			this->addNode(node->right, bid);
			}
	}
}



void BinarySearchTree::inOrder(Node* node) {
	if (node != nullptr) {
			inOrder(node->left);
			cout << node->bid.bidId << ": " << node->bid.title << " | "
			     << node->bid.amount << " | "
			     << node->bid.fund << endl;
			inOrder(node->right);
		}
}

Node* BinarySearchTree::removeNode(Node* node, string bidId) {
	// if this tree is empty then return (avoid crashing)
	if (node == nullptr) {
		return node;
	}

	// recurse down left subtree
	if (bidId.compare(node->bid.bidId) < 0) {
		node->left = removeNode(node->left, bidId);
	} else if (bidId.compare(node->bid.bidId) > 0) {
		node->right = removeNode(node->right, bidId);
	} else {
		// no children so this is a leaf node
		if (node->left == nullptr && node->right == nullptr) {
			delete node;
			node = nullptr;
		}
		// one child to the left
		else if (node->left != nullptr && node->right == nullptr) {
			Node* temp = node;
			node = node->left;
			delete temp;
		}
		else if (node->right != nullptr && node->left == nullptr) {
			Node* temp = node;
			node = node->right;
			delete temp;
		}
		// two children
		else {
			Node* temp = node->right;
			while (temp->left != nullptr) {
				temp = temp->left;
			}
			node->bid = temp->bid;
			node->right = removeNode(node->right, temp->bid.bidId);
		}
	}
	return node;
}

//============================================================================
// Static methods used for testing
//============================================================================

/**
 * Display the bid information to the console (std::out)
 *
 * @param bid struct containing the bid info
 */
void displayBid(Bid bid) {
    cout << bid.bidId << ": " << bid.title << " | " << bid.amount << " | "
            << bid.fund << endl;
    return;
}

/**
 * Load a CSV file containing bids into a container
 *
 * @param csvPath the path to the CSV file to load
 * @return a container holding all the bids read
 */
void loadBids(string csvPath, BinarySearchTree* bst) {
    cout << "Loading CSV file " << csvPath << endl;

    // initialize the CSV Parser using the given path
    csv::Parser file = csv::Parser(csvPath);

    // read and display header row - optional
    vector<string> header = file.getHeader();
    for (auto const& c : header) {
        cout << c << " | ";
    }
    cout << "" << endl;

    try {
        // loop to read rows of a CSV file
        for (unsigned int i = 0; i < file.rowCount(); i++) {

            // Create a data structure and add to the collection of bids
            Bid bid;
            bid.bidId = file[i][1];
            bid.title = file[i][0];
            bid.fund = file[i][8];
            bid.amount = strToDouble(file[i][4], '$');

            //cout << "Item: " << bid.title << ", Fund: " << bid.fund << ", Amount: " << bid.amount << endl;

            // push this bid to the end
            bst->Insert(bid);
        }
    } catch (csv::Error &e) {
        std::cerr << e.what() << std::endl;
    }
}



/**
 * Prompt user for bid information using console (std::in)
 *
 * @return Bid struct containing the bid info
 */
Bid getBid() {
    Bid bid;

    cout << "Enter Id: ";
    cin.ignore();
    getline(cin, bid.bidId);

    cout << "Enter title: ";
    getline(cin, bid.title);

    cout << "Enter fund: ";
    cin >> bid.fund;

    cout << "Enter amount: ";
    cin.ignore();
    string strAmount;
    getline(cin, strAmount);
    bid.amount = strToDouble(strAmount, '$');

    return bid;
}


/**
 * Load a CSV file containing bids into a container
 *
 * @param csvPath the path to the CSV file to load
 * @return a container holding all the bids read
 */
vector<Bid> loadBids(string csvPath) {
    cout << "Loading CSV file " << csvPath << endl;

    // Define a vector data structure to hold a collection of bids.
    vector<Bid> bids;

    // initialize the CSV Parser using the given path
    csv::Parser file = csv::Parser(csvPath);

    try {
        // loop to read rows of a CSV file
        for (int i = 0; i < file.rowCount(); i++) {

            // Create a data structure and add to the collection of bids
            Bid bid;
            bid.bidId = file[i][1];
            bid.title = file[i][0];
            bid.fund = file[i][8];
            bid.amount = strToDouble(file[i][4], '$');

            //cout << "Item: " << bid.title << ", Fund: " << bid.fund << ", Amount: " << bid.amount << endl;

            // push this bid to the end
            bids.push_back(bid);
        }
    } catch (csv::Error &e) {
        std::cerr << e.what() << std::endl;
    }
    return bids;
}
/**
 * Partition the vector of bids into two parts, low and high
 *
 * @param bids Address of the vector<Bid> instance to be partitioned
 * @param begin Beginning index to partition
 * @param end Ending index to partition
 */
int partition(vector<Bid>& bids, int begin, int end) {
	int low = begin;
	int high = end;

	// pick the middle element as the pivit point
	int pivot = begin + (end - begin) / 2;
	bool done = false;

	while(!done){

		// keep incrementing low as long as it's less than pivot
		while (bids.at(low).title.compare(bids.at(pivot).title) < 0) {
			++low;
		}
		// keep decrementing high as long as it is less than pivot
		while (bids.at(pivot).title.compare(bids.at(high).title) < 0) {
			--high;
		}

		if (low >= high){
			done = true;
		}else{
			// swap the low and high bids using built-in vector method
			swap(bids.at(low), bids.at(high));

			// move end points closer
			++low;
			--high;
		}
	}
	return high;
}

/**
 * Perform a quick sort on bid title
 * Average performance: O(n log(n))
 * Worst case performance O(n^2))
 *
 * @param bids address of the vector<Bid> instance to be sorted
 * @param begin the beginning index to sort on
 * @param end the ending index to sort on
 */
void quickSort(vector<Bid>& bids, int begin, int end) {
	int mid = 0;

	// If zero or one bids to sort, then done
	if(begin >= end){
		return;
	}

	// partition bids into low and high parts
	mid = partition(bids, begin, end);

	// recursive call quicksort using midpoint value (begin to midpoint)
	quickSort(bids, begin, mid);

	// recursive call quicksort using midpoint value (begin to midpoint)
	quickSort(bids, mid+1, end);
}

/**
 * Perform a selection sort on bid title
 * Average performance: O(n^2))
 * Worst case performance O(n^2))
 *
 * @param bid address of the vector<Bid>
 *            instance to be sorted
 */
void selectionSort(vector<Bid>& bids) {
	int min;

	// pos is the position within the bids that marks sorted/unsorted
	for (unsigned pos = 0; pos < bids.size(); ++pos) {
		min = pos;

		for (unsigned j = pos+1; j < bids.size(); ++j) {
			if (bids.at(j).title.compare(bids.at(min).title) < 0) {
				min = j;
			}
		}

		if(min != pos) {
			swap(bids.at(pos), bids.at(min));
		}
	}
}

/**
 * Simple C function to convert a string to a double
 * after stripping out unwanted char
 *
 * credit: http://stackoverflow.com/a/24875936
 *
 * @param ch The character to strip out
 */
double strToDouble(string str, char ch) {
    str.erase(remove(str.begin(), str.end(), ch), str.end());
    return atof(str.c_str());
}

/**
 * The one and only main() method
 */
int main(int argc, char* argv[]) {

    // process command line arguments
    string csvPath, bidKey;
    switch (argc) {
    case 2:
        csvPath = argv[1];
        bidKey = "98109";
        break;
    case 3:
        csvPath = argv[1];
        bidKey = argv[2];
        break;
    default:
        csvPath = "eBid_Monthly_Sales_Dec_2016.csv";
        bidKey = "98109";
    }


    // Define a vector to hold all the bids
       vector<Bid> bids;

    // Define a timer variable
    clock_t ticks;

    // Define a binary search tree to hold all bids
    BinarySearchTree* bst;

    Bid bid;

    int choice = 0;
    while (choice != 9) {
        cout << "Menu:" << endl;
        cout << "  1. Load Bids - Binary Search Tree" << endl;
        cout << "  2. Display All Bids - Binary Search Tree" << endl;
        cout << "  3. Find Bid - Binary Search Tree" << endl;
        cout << "  4. Remove Bid - Binary Search Tree" << endl;
        cout << "  5. Load Bids - Vector Representation" << endl;
        cout << "  6. Display All Bids - Vector Representation" << endl;
        cout << "  7. Selection Sort - Vector Representation" << endl;
        cout << "  8. Quick Sort - Vector Representation" << endl;
        cout << "  9. Exit" << endl;
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {

        case 1:
            bst = new BinarySearchTree();

            // Initialize a timer variable before loading bids
            ticks = clock();

            // Complete the method call to load the bids
            loadBids(csvPath, bst);

            //cout << bst->Size() << " bids read" << endl;

            // Calculate elapsed time and display result
            ticks = clock() - ticks; // current clock ticks minus starting clock ticks
            cout << "time: " << ticks << " clock ticks" << endl;
            cout << "time: " << ticks * 1.0 / CLOCKS_PER_SEC << " seconds" << endl;
            break;

        case 2:
            bst->InOrder();
            break;

        case 3:
            ticks = clock();

            bid = bst->Search(bidKey);

            ticks = clock() - ticks; // current clock ticks minus starting clock ticks

            if (!bid.bidId.empty()) {
                displayBid(bid);
            } else {
            	cout << "Bid Id " << bidKey << " not found." << endl;
            }

            cout << "time: " << ticks << " clock ticks" << endl;
            cout << "time: " << ticks * 1.0 / CLOCKS_PER_SEC << " seconds" << endl;

            break;

        case 4:
            bst->Remove(bidKey);
            break;

        case 5:
        	// Initialize a timer variable before loading bids
        	ticks = clock();

        	// Complete the method call to load the bids
        	bids = loadBids(csvPath);

        	cout << bids.size() << " bids read" << endl;

        	// Calculate elapsed time and display result
        	ticks = clock() - ticks; // current clock ticks minus starting clock ticks
        	cout << "time: " << ticks << " clock ticks" << endl;
        	cout << "time: " << ticks * 1.0 / CLOCKS_PER_SEC << " seconds" << endl;

        	break;

        case 6:
            // Loop and display the bids read
            for (int i = 0; i < bids.size(); ++i) {
                displayBid(bids[i]);
            }
            cout << endl;

            break;


        case 7:
            // Initialize a timer variable before loading bids
            ticks = clock();

            // Complete the method call to load the bids
            selectionSort(bids);

            cout << bids.size() << " bids read" << endl;

            // Calculate elapsed time and display result
            ticks = clock() - ticks; // current clock ticks minus starting clock ticks
            cout << "time: " << ticks << " clock ticks" << endl;
            cout << "time: " << ticks * 1.0 / CLOCKS_PER_SEC << " seconds" << endl;

            break;



        case 8:
            // Initialize a timer variable before loading bids
            ticks = clock();

            // Complete the method call to load the bids
            quickSort(bids, 0, bids.size()-1);

            cout << bids.size() << " bids read" << endl;

            // Calculate elapsed time and display result
            ticks = clock() - ticks; // current clock ticks minus starting clock ticks
            cout << "time: " << ticks << " clock ticks" << endl;
            cout << "time: " << ticks * 1.0 / CLOCKS_PER_SEC << " seconds" << endl;

            break;
        }
    }

    cout << "Good bye." << endl;

	return 0;
}



```

### Artifact Three - Database

  This artifact was created in December of 2019 for the assignment in which we were to create, read, update, and delete information from mongoDB using the pymongo interface. This artifact came from cs-340: Advanced Programming Concepts or now named Client/Server programming.
  This item was selected because it aligns closely with the manipulation of mongoDB using the python programming language. I wanted to show that I know basic python with the pymongo library added on. This example was more concrete than a lot of other examples that I was looking at. Many of the assignments in the databases classes required working in the codio environment and taking screen captures of the individual commands that were required to answer questions. That way of evaluating our progress doesn’t show much in the way of complete artifacts that showcase the skills that we built. The artifact was improved by adding additional functions to the pymango framework and by adding tests to the main program which use those new functions. Between the new and old functions this short program demonstrates the ability to solve problems involving storing, manipulating, and accessing data.
  This enhancement changed a great deal since module one. I was originally going to use a MySQL script from my Introduction to SQL class but the class had been archived which caused me to lose access to the testing environment. Thus I am updating my outcome-coverage plan to include the pymango program.
  When I reflect on enhancing the artifact I realize that I had to relearn how to use mongoDB and pymongo. The original class moved very quickly through the material and it was a challenge to remember how to do anything with mongoDB. After several tries getting the syntax correct the interface became more straightforward. There are not a lot of internet resources that use the provided bottle library of scripts so finding references to help me was difficult.

```
# by Matthew Malone matthew.malone1@snhu.edu
# July 24, 2020
# For CS-499 Computer Science Capstone
# This program implements the CRUD functions using pymongo.
# These functions are then enhanced by fifty, industry, and aggregate
# producing a small but solid pymango database interface.
#

# Data forthis is in json format. The mongo client must be running
# and have access to the referenced database. In this case that
# is stocks with the document market.
import json
from bson import json_util
from bson.son import SON
from pymongo import MongoClient

connection = MongoClient('localhost', 27017)
db = connection['market']
collection = db['stocks']


# Create an entry in the referenced database.
def create(document):
  try:
    result=collection.insert_one(document)
  except ValidationError as ve:
    abort(400,str(ve))
  return result

# Find a document from the database.
def read(document):
  try:
    result = collection.find_one(document)
  except ValidationError as ve:
    abort(400,str(ve))
  return result
  
# Update an entry in the referenced database.  
def update(document, upd):
  try:
    result= collection.update_one(document, upd)
  except ValidationError as ve:
    abort(400,str(ve))
  return result

# Delete a document from the referenced database.
def delete(document):
  try:
    result = collection.delete_one(document)
  except ValidationError as ve:
    abort(400,str(ve))
  return result
  

# Get the fifty day moving average between specified low and high  
def fifty(low, high):
  result = collection.find({"50-Day Simple Moving Average": {"$gte":low, "$lte": high}})
  result_count = result.count()
  return result_count

# find the specified industry items.
def industry(stuff):
  result = collection.find({"Industry": stuff},{"Ticker": 1})
  return result

# Find all the items matching the specified general category of stock.
def aggregate(morestuff):
  pipeline = [ { "$match": { "Sector": morestuff }},
              { "$group": {"_id" : "$Industry", "Shares Outstanding":{"$sum":"$Shares Outstanding"}}},
              { "$sort": SON([("Sector", 1), ("Industry", 1)])}]
  result = collection.aggregate(pipeline)
  return result


def main():

	#basic document setup to test create update and delete
    myDocument = {"Ticker":"Test Stock"}
    myDocument2 = {"Volume": 1851234}
    print create(myDocument)
    print read(myDocument)
    print update(myDocument, {"$set":myDocument2})
    print delete(myDocument)

    # Calling the enhancement functions.
    # Setting the 50 day simple moving average range.
    print fifty(0, 1)

    # Find everything in the Medical laboritories and research category.
    for x in industry("Medical Laboratories & Research"):
      print(x)

    # Find everything in healthcare.
    for x in aggregate("Healthcare"):
      print(x)
main()

```


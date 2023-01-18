
## Struct Definition:
```c
struct cart {
	struct cart_item* products;
}

struct cart_item {
	char* name;
	char* price; // Might just be an int, but the prices go in as (char*) 199
	struct cart_item next;
	struct cart_item prev;
}
```

## Regular functions
### **Create**
This seems to make a product that goes into cart.
- Takes a string and char as a parameter, 199 - 399

### **Insert**
This seems to take a product and attach it to the (end?) cart:
- Takes a product and adds it to the end of the cart. Here's some pseudocode.
```c
void insert(struct cart_item* item) {
    // Starting at the first item in the cart
    struct cart_item* current = myCart;

    // Iterate through the cart until we find the last item
    while (current->next != NULL) {
        current = current->next;
    }

    // Set the next item in the cart to the item being inserted
    current->next = item;

    // Set the previous item of the item being inserted to the last item in the cart
    item->prev = current;
}
```

### **Add** 
This function just adds a product into the cart linked list.
The products are just items that look like:

```c
struct cart_item {
	char* name;
	char* price; // Might just be an int, but the prices go in as (char*) 199
	struct cart_item next;
	struct cart_item prev;
}
```

### **Cart**
Legit just adds up the int total of the cart items. 

### Delete

This function is used to remove an item from the cart. It prompts the user for the number of the item they wish to remove, reads the input and converts it to an integer. Then it loops through the cart and checks if the counter, which starts at 1 and is incremented for each iteration, matches the user's input. If it does, it saves a reference to the next and previous items in the cart, updates the next and previous pointers of the surrounding items and print a message indicating the item removed. The function loops until the next item is NULL, meaning that it reached the end of the cart.

This code has a **bug that causes an infinite loop when the user inputs an item number that does not exist in the cart**. This is because the loop is controlled by a do-while loop that always evaluates to true, so it will never exit the loop if it doesn't find the item number. This can cause the program to hang and never return control to the user.

Another bug is that the function doesn't free the memory allocated for the item that is being removed. This can lead to a memory leak, where the program uses up more and more memory over time.

Additionally, **the function doesn't check if the input is out of range**, so the user could input a number that is less than 1 or greater than the number of items in the cart, leading to unexpected behavior.

Rewritten function:

```c
void delete(void)
{
    cart_item *current;
    cart_item *next;
    cart_item *prev;
    int userInput;
    int counter;
    cart_item *item;
    char userBuf[22];

    counter = 1;
    item = myCart.next;
    printf("Item Number> ");
    fflush(stdout);
    my_read(userBuf, 0x15);
    userInput = atoi(userBuf);
    //loop through the cart
    do {
        if (item == NULL) {
            return;
        }
        if (counter == userInput) {
            // Save a reference to the next and previous items
            next = item->next;
            prev = item->prev;
            // Update the next and previous pointers of the surrounding items
            if (prev != NULL) {
                prev->next = next;
            }
            if (next != NULL) {
                next->prev = prev;
            }
            printf("Remove %d:%s from your shopping cart.\n", counter, item->name);
        }
        counter++;
        item = item->next;
    } while (true);
}
```


## Interesting functions:

Delete
* **the function doesn't check if the input is out of range**
* **bug that causes an infinite loop when the user inputs an item number that does not exist in the cart**

Checkout
* Something weird I noticed was that item.name is on the stack.
* Also, the asprintf function *might* free its value once it leaves the function.

```c
void checkout(void) {
  int iVar1;
  int in_GS_OFFSET;
  int total;
  cart_item item;
  
  iVar1 = *(int *)(in_GS_OFFSET + 0x14);
  total = cart();
  if (total == 0x1c06) {
    puts("*: iPhone 8 - $1");
    asprintf(&item.name,"%s","iPhone 8");
    item.price = 1;
    insert(&item);
    total = 0x1c07;
  }
  printf("Total: $%d\n",total);
  puts("Want to checkout? Maybe next time!");
  if (iVar1 != *(int *)(in_GS_OFFSET + 0x14)) {
    __stack_chk_fail();
  }
  return;
}
```
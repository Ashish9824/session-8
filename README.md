# session-8

### Task 1: `getUserInitials` Function

```c
#include <stdio.h>
#include <ctype.h>
#include <string.h>

// Function Declaration
void getUserInitials(const char *fullName, char *initials);

int main() {
    char cricketerName[] = "Virat Kohli";
    char initials[10];

    // Function Call
    getUserInitials(cricketerName, initials);

    printf("Full Name : %s\n", cricketerName);
    printf("Initials  : %s\n", initials);

    return 0;
}

// Function Definition
void getUserInitials(const char *fullName, char *initials) {
    int idx = 0;
    int isNewWord = 1;

    for (int i = 0; fullName[i] != '\0'; i++) {
        // If a non-space character comes after a space/start, it is an initial
        if (!isspace(fullName[i]) && isNewWord) {
            initials[idx++] = toupper(fullName[i]);
            isNewWord = 0;
        } else if (isspace(fullName[i])) {
            isNewWord = 1;
        }
    }
    initials[idx] = '\0'; // Null-terminate the output string
}

```

---

### Task 2: `addToCart` with Pass-by-Reference

In C, passing an array to a function decays it into a pointer to its first element (pass-by-reference behavior), which directly modifies the caller's array.

```c
#include <stdio.h>
#include <string.h>

#define MAX_ITEMS 10
#define NAME_LEN 50

// Function Declaration: Cart array and pointer to itemCount are passed by reference
void addToCart(char cart[][NAME_LEN], int *itemCount, const char *productName);

int main() {
    char cart[MAX_ITEMS][NAME_LEN];
    int itemCount = 0;

    printf("Cart item count before function call: %d\n", itemCount);

    // Function Calls
    addToCart(cart, &itemCount, "Mechanical Keyboard");
    addToCart(cart, &itemCount, "Wireless Mouse");
    addToCart(cart, &itemCount, "Gaming Headset");

    // Demonstrating persistence outside the function scope
    printf("\n--- Final Cart Items Outside Function ---\n");
    for (int i = 0; i < itemCount; i++) {
        printf("%d. %s\n", i + 1, cart[i]);
    }
    printf("Cart item count outside function: %d\n", itemCount);

    return 0;
}

// Function Definition
void addToCart(char cart[][NAME_LEN], int *itemCount, const char *productName) {
    if (*itemCount < MAX_ITEMS) {
        strcpy(cart[*itemCount], productName);
        (*itemCount)++; // Modifies original itemCount directly via pointer

        printf("Added '%s' to cart. (Total items now: %d)\n", productName, *itemCount);
    } else {
        printf("Cart is full!\n");
    }
}

```

---

### Task 3: `increaseFollowersByValue` vs `increaseFollowersByReference`

```c
#include <stdio.h>

// Pass-by-value: Operates on an isolated copy; original variable is unchanged
void increaseFollowersByValue(int followers) {
    followers += 1000;
    printf("Inside increaseFollowersByValue: %d\n", followers);
}

// Pass-by-reference: Operates directly on the memory address via a pointer
void increaseFollowersByReference(int *followers) {
    *followers += 1000;
    printf("Inside increaseFollowersByReference: %d\n", *followers);
}

int main() {
    int followerCount = 5000;

    printf("Initial follower count: %d\n\n", followerCount);

    // 1. Pass-by-value demonstration
    printf("--- Testing Pass-by-Value ---\n");
    increaseFollowersByValue(followerCount);
    printf("Follower count in main() after call: %d (Unchanged)\n\n", followerCount);

    // 2. Pass-by-reference demonstration
    printf("--- Testing Pass-by-Reference ---\n");
    increaseFollowersByReference(&followerCount);
    printf("Follower count in main() after call: %d (Updated)\n", followerCount);

    return 0;
}

```

---

### Task 4: `formatPrice` Function (Indian Rupee Formatting)

Formats integer amounts following the Indian numbering system standard (e.g., `1599` $\rightarrow$ `₹1,599` and `125000` $\rightarrow$ `₹1,25,000`):

```c
#include <stdio.h>
#include <string.h>

// Formats an integer amount into an Indian currency string with ₹ symbol and commas
void formatPrice(long long price, char *output) {
    char temp[32];
    sprintf(temp, "%lld", price);

    int len = strlen(temp);
    char formatted[64];
    int fIdx = 0;

    // Handle numbers with 3 or fewer digits
    if (len <= 3) {
        sprintf(output, "₹%s", temp);
        return;
    }

    // Copy last 3 digits
    char lastThree[4];
    strncpy(lastThree, &temp[len - 3], 3);
    lastThree[3] = '\0';

    // Group the preceding digits into pairs of two
    int remaining = len - 3;
    for (int i = 0; i < remaining; i++) {
        formatted[fIdx++] = temp[i];
        if ((remaining - 1 - i) % 2 == 0 && (i != remaining - 1)) {
            formatted[fIdx++] = ',';
        }
    }
    formatted[fIdx++] = ',';
    formatted[fIdx] = '\0';

    strcat(formatted, lastThree);
    sprintf(output, "₹%s", formatted);
}

int main() {
    char tag1[64], tag2[64], tag3[64];

    formatPrice(1599, tag1);
    formatPrice(24999, tag2);
    formatPrice(129990, tag3);

    printf("--- Flipkart Product Price Tags ---\n");
    printf("Product 1 (Noise Cancelling Headphones) : %s\n", tag1);
    printf("Product 2 (Smart Monitor)              : %s\n", tag2);
    printf("Product 3 (Gaming Laptop)               : %s\n", tag3);

    return 0;
}

```

---

### Task 5: Refactored Reusable Capitalization Function

This function capitalizes the first character of each word in any arbitrary string, working identically for product titles, full names, and usernames:

```c
#include <stdio.h>
#include <ctype.h>
#include <string.h>

// Reusable function: Capitalizes each word in any input string
void toTitleCase(const char *input, char *output) {
    int isStartOfWord = 1;

    for (int i = 0; input[i] != '\0'; i++) {
        if (isspace(input[i])) {
            output[i] = input[i];
            isStartOfWord = 1;
        } else {
            if (isStartOfWord) {
                output[i] = toupper(input[i]);
                isStartOfWord = 0;
            } else {
                output[i] = tolower(input[i]);
            }
        }
    }
    output[strlen(input)] = '\0';
}

int main() {
    char productRaw[] = "wireless gaming mouse with rgb";
    char usernameRaw[] = "ashish_sahu rohit_suthar";
    char cricketRaw[] = "ms dhoni";

    char formattedProduct[100];
    char formattedUsername[100];
    char formattedCricketer[100];

    toTitleCase(productRaw, formattedProduct);
    toTitleCase(usernameRaw, formattedUsername);
    toTitleCase(cricketRaw, formattedCricketer);

    printf("Formatted Product  : %s\n", formattedProduct);
    printf("Formatted Username : %s\n", formattedUsername);
    printf("Formatted Name     : %s\n", formattedCricketer);

    return 0;
}

```

// this code is to take input from serial monitor, interpret expression and then give output to analog modules to perform operation
  #include <Arduino.h>

  const int MAX_SIZE = 50; 
  const int MAX_EXPRESSION_LENGTH = 50; // Maximum length of the expression

  template<typename T>
  class Stack {
  private:
      T data[MAX_SIZE]; // Array to store stack elements
      int top; // Index of the top element

  public:
      Stack() : top(-1) {} // Constructor to initialize an empty stack

      bool isEmpty() {
          return top == -1; // Stack is empty if top is -1
      }

      bool isFull() {
          return top == MAX_SIZE - 1; // Stack is full if top is equal to the maximum size
      }

      void push(T element) {
          if (isFull()) {
              Serial.println("Stack Overflow! Cannot push element.");
              return;
          }
          data[++top] = element; // Increment top and insert element into the stack
      }

      T pop() {
          if (isEmpty()) {
              Serial.println("Stack Underflow! Cannot pop element.");
              return T(); // Return default value of type T
          }
          return data[top--]; // Decrement top and return the element
      }

      T peek() {
          if (isEmpty()) {
              Serial.println("Stack is empty! No top element.");
              return T(); // Return default value of type T
          }
          return data[top]; // Return the top element without removing it
      }
  };

  Stack<float> operators; // Stack for operators (now for float numbers)
  char postfix[MAX_EXPRESSION_LENGTH]; // Array to store postfix expression
  int postfixIndex = 0; // Index for postfix expression array

  // Precedence function with caret operator
  int precedence(char operate) {
      if (operate == '^') // Higher precedence for caret operator
          return 4;
      else if (operate == '*' || operate == '/')
          return 3;
      else if (operate == '+' || operate == '-')
          return 2;
      else if (operate == '(')
          return 1;
      else
          return 0;
  }

  // Function to check if character is an operand
  bool isOperand(char x) {
      return (x >= '0' && x <= '9') || x == '.'; // Allow digits and decimal point
  }

  // Function to convert infix to postfix
  void InfixtoPostfix(String s) {
      int length = s.length();
      postfixIndex = 0; // Reset postfix index

      for (int i = 0; i < length; i++) {
          char c = s[i];
          if (isOperand(c)) {
              postfix[postfixIndex++] = c;
              // Check for multi-digit numbers or floats
              while (i + 1 < length && (isOperand(s[i + 1]) || s[i + 1] == '.')) {
                  postfix[postfixIndex++] = s[++i];
              }
              postfix[postfixIndex++] = ' '; // Add space after each operand
          } else if (c == '(') {
              operators.push(c);
          } else if (c == ')') {
              while (!operators.isEmpty() && operators.peek() != '(') {
                  postfix[postfixIndex++] = operators.pop();
                  postfix[postfixIndex++] = ' '; // Add space after each operator
              }
              operators.pop(); // Pop '(' from stack
          } else { // Operator
              while (!operators.isEmpty() && precedence(operators.peek()) >= precedence(c)) {
                  postfix[postfixIndex++] = operators.pop();
                  postfix[postfixIndex++] = ' '; // Add space after each operator
              }
              operators.push(c);
          }
      }

      // Pop remaining operators from stack
      while (!operators.isEmpty()) {
          postfix[postfixIndex++] = operators.pop();
          postfix[postfixIndex++] = ' '; // Add space after each operator
      }
  }

  // Function to evaluate the postfix expression
  float evaluate() {
      Stack<float> operands; // Stack for operands (now for float numbers)

      char* endPtr; // Pointer for strtod function

      for (int i = 0; i < postfixIndex; i++) {
          char c = postfix[i];
          if (isOperand(c)) {
              // Convert multi-digit numbers or floats to float
              float num = atof(&postfix[i]);
              operands.push(num);
              while (isOperand(postfix[i]) || postfix[i] == '.') {
                  i++; // Skip the rest of the number
              }
              i--; // Adjust index
          } else if (c != ' ') { // Skip spaces
              // Operator
              float operand2 = operands.pop();
              float operand1 = operands.pop();
              switch (c) {
                  case '+':
                      Serial.println("plus");
                      //operands.push(operand1 + operand2);
                      break;
                  case '-':
                      Serial.println("sub");
                      //operands.push(operand1 - operand2);
                      break;
                  case '*':
                      Serial.println("mul");
                      //operands.push(operand1 * operand2);
                      break;
                  case '/':
                      Serial.println("div");
                      //operands.push(operand1 / operand2);
                      break;
                  case '^':
                      Serial.println("power");
                      //operands.push(pow(operand1, operand2)); // Exponentiation
                      break;
                  default:
                      // Handle unsupported operators
                      Serial.println("Unsupported operator!");
                      return 0.0;
              }
          }
      }
      return operands.pop();
  }

  void setup() {
      Serial.begin(9600);
  }

  void loop() {
      if (Serial.available()) {
          String exp = Serial.readStringUntil('\n'); // Read until newline character
          Serial.println(exp);

          InfixtoPostfix(exp);

          // Output the postfix expression
          Serial.print("Postfix expression: ");
          for (int i = 0; i < postfixIndex; i++) {
              Serial.print(postfix[i]);
              Serial.print(" ");
          }
          Serial.println();

          // Evaluate the postfix expression
          float result = evaluate();
          Serial.print("Result: ");
          Serial.println(result);
      }
  }

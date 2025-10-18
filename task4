#include <iostream>
#include <string>
#include <stack>
#include <cmath>
#include <sstream>

// --- Phase 1 & 2: Tokenization and Shunting-Yard ---

// Helper function to determine operator precedence
int getPrecedence(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    return 0; // For parentheses and other non-operators
}

// Helper function to check if a character is an operator
bool isOperator(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/';
}

/**
 * @brief Converts an infix arithmetic expression string into a queue of tokens in Reverse Polish Notation (RPN).
 * @param infix The input arithmetic expression (e.g., "1 + 2 * 3").
 * @param rpnOutput Queue to store the output tokens in RPN order.
 */
void shuntingYard(const std::string& infix, std::stack<std::string>& rpnOutput) {
    std::stack<char> opStack; // Operator stack
    
    for (int i = 0; i < infix.length(); ++i) {
        char c = infix[i];

        if (c == ' ') continue; // Skip spaces

        if (isdigit(c) || c == '.') {
            // Read entire number
            std::string num;
            while (i < infix.length() && (isdigit(infix[i]) || infix[i] == '.')) {
                num += infix[i];
                i++;
            }
            rpnOutput.push(num);
            i--; // Step back to re-evaluate the character after the number
        } 
        else if (isOperator(c)) {
            while (!opStack.empty() && isOperator(opStack.top()) &&
                   getPrecedence(opStack.top()) >= getPrecedence(c)) {
                
                std::string op(1, opStack.top());
                rpnOutput.push(op);
                opStack.pop();
            }
            opStack.push(c);
        }
        else if (c == '(') {
            opStack.push(c);
        }
        else if (c == ')') {
            while (!opStack.empty() && opStack.top() != '(') {
                std::string op(1, opStack.top());
                rpnOutput.push(op);
                opStack.pop();
            }
            if (opStack.empty()) {
                throw std::runtime_error("Mismatched parentheses.");
            }
            opStack.pop(); // Pop the '('
        }
        else {
            throw std::runtime_error("Invalid character in expression.");
        }
    }

    // Pop any remaining operators from the stack to the output
    while (!opStack.empty()) {
        if (opStack.top() == '(') {
            throw std::runtime_error("Mismatched parentheses.");
        }
        std::string op(1, opStack.top());
        rpnOutput.push(op);
        opStack.pop();
    }
}


// --- Phase 3: RPN Evaluation ---

/**
 * @brief Performs the arithmetic operation.
 * @param op Operator character.
 * @param b Right operand.
 * @param a Left operand.
 * @return Result of the operation.
 */
double applyOp(char op, double b, double a) {
    switch (op) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/': 
            if (b == 0) throw std::runtime_error("Division by zero.");
            return a / b;
    }
    return 0; 
}

/**
 * @brief Evaluates an expression represented in Reverse Polish Notation (RPN).
 * @param rpnTokens Stack containing the RPN tokens (from Shunting-Yard, bottom-to-top).
 * @return The final calculated result.
 */
double evaluateRPN(std::stack<std::string>& rpnTokens) {
    std::stack<double> valueStack;
    
    // Reverse the order of tokens for standard RPN processing (queue-like behavior)
    std::stack<std::string> reversedTokens;
    while (!rpnTokens.empty()) {
        reversedTokens.push(rpnTokens.top());
        rpnTokens.pop();
    }

    while (!reversedTokens.empty()) {
        std::string token = reversedTokens.top();
        reversedTokens.pop();

        if (isdigit(token[0]) || (token.length() > 1 && isdigit(token[1]))) {
            // Token is a number
            valueStack.push(std::stod(token));
        } else if (isOperator(token[0]) && token.length() == 1) {
            // Token is an operator
            if (valueStack.size() < 2) {
                throw std::runtime_error("Malformed expression: insufficient operands.");
            }
            double b = valueStack.top(); valueStack.pop();
            double a = valueStack.top(); valueStack.pop();
            
            valueStack.push(applyOp(token[0], b, a));
        }
    }

    if (valueStack.size() != 1) {
        throw std::runtime_error("Malformed expression: too many operands.");
    }
    
    return valueStack.top();
}

// --- Main Program ---

int main() {
    std::string input;
    std::cout << "--- CODTECH Simple Compiler/Evaluator ---\n";
    std::cout << "Enter an arithmetic expression (e.g., 5 + 3 * (10 - 2) / 4): \n> ";
    std::getline(std::cin, input);

    try {
        // Phase 1 & 2: Tokenization and Parsing (Shunting-Yard)
        std::stack<std::string> rpnTokens;
        shuntingYard(input, rpnTokens);

        // Display RPN (optional step for debugging/demonstration)
        std::stack<std::string> tempStack = rpnTokens;
        std::string rpnStr = "";
        while (!tempStack.empty()) {
             rpnStr = tempStack.top() + " " + rpnStr; // Reconstruct RPN string
             tempStack.pop();
        }
        std::cout << "Parsed RPN: " << rpnStr << std::endl;

        // Phase 3: Evaluation
        double result = evaluateRPN(rpnTokens);

        std::cout << "\n=====================================\n";
        std::cout << "Input Expression: " << input << std::endl;
        std::cout << "Evaluation Result: " << result << std::endl;
        std::cout << "=====================================\n";

    } catch (const std::runtime_error& e) {
        std::cerr << "Evaluation Error: " << e.what() << std::endl;
    } catch (...) {
        std::cerr << "An unknown error occurred during parsing or evaluation." << std::endl;
    }

    return 0;
}

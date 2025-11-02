# Dynamic-programming-find-the-closest-pair-of-points 
#include <iostream>
#include <vector>
#include <algorithm>
#include <limits>

using namespace std;

// Function to print the optimal binary search tree
void printOptimalBST(const vector<vector<int>>& root, int i, int j, const vector<int>& keys, int parent = -1) {
    if (i > j) return;

    int r = root[i][j];
    if (parent == -1)
        cout << "Root of tree: " << keys[r] << endl;
    else if (keys[r] < keys[parent])
        cout << "Left child of " << keys[parent] << ": " << keys[r] << endl;
    else
        cout << "Right child of " << keys[parent] << ": " << keys[r] << endl;

    printOptimalBST(root, i, r - 1, keys, r); // Left subtree
    printOptimalBST(root, r + 1, j, keys, r); // Right subtree
}

// Function to find the optimal binary search tree
pair<double, vector<vector<int>>> optimalBST(const vector<int>& keys, const vector<double>& probabilities, int n) {
    vector<vector<double>> cost(n, vector<double>(n, 0));
    vector<vector<int>> root(n, vector<int>(n, 0));

    vector<double> cumulative_prob(n + 1, 0);
    for (int i = 1; i <= n; ++i)
        cumulative_prob[i] = cumulative_prob[i - 1] + probabilities[i - 1];

    // Base cases: single keys
    for (int i = 0; i < n; ++i) {
        cost[i][i] = probabilities[i];
        root[i][i] = i;
    }

    // Build up the solution for chains of length 2 to n
    for (int length = 2; length <= n; ++length) {
        for (int i = 0; i <= n - length; ++i) {
            int j = i + length - 1;
            cost[i][j] = numeric_limits<double>::max();

            for (int r = i; r <= j; ++r) {
                double left_cost = (r > i) ? cost[i][r - 1] : 0;
                double right_cost = (r < j) ? cost[r + 1][j] : 0;
                double total_cost = left_cost + right_cost + (cumulative_prob[j + 1] - cumulative_prob[i]);

                if (total_cost < cost[i][j]) {
                    cost[i][j] = total_cost;
                    root[i][j] = r;
                }
            }
        }
    }

    return {cost[0][n - 1], root};
}

int main() {
    int n;
    cout << "Enter number of keys: ";
    cin >> n;

    vector<int> keys(n);
    vector<double> probabilities(n);

    cout << "Enter the keys: ";
    for (int i = 0; i < n; ++i) {
        cin >> keys[i];
    }

    cout << "Enter the probabilities associated with the keys: ";
    for (int i = 0; i < n; ++i) {
        cin >> probabilities[i];
    }

    // Sorting the keys and adjusting the probabilities
    vector<pair<int, double>> key_prob_pairs(n);
    for (int i = 0; i < n; ++i) {
        key_prob_pairs[i] = {keys[i], probabilities[i]};
    }

    sort(key_prob_pairs.begin(), key_prob_pairs.end());

    for (int i = 0; i < n; ++i) {
        keys[i] = key_prob_pairs[i].first;
        probabilities[i] = key_prob_pairs[i].second;
    }

    // Compute the optimal BST
    auto [optimal_cost, root] = optimalBST(keys, probabilities, n);

    cout << "\nOptimal cost of binary search tree: " << optimal_cost << endl;

    cout << "Optimal binary search tree structure:\n";
    printOptimalBST(root, 0, n - 1, keys);

    return 0;
}
[11/2, 3:50 PM] Shaik Abdulla: Exp-8
use dynamic programming to find the optimal binary search tree for a given set of numbers together with their probabilities.Remember tha the numbers may be generated in any order,so a presorting step is also required

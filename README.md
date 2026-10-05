/* DSA Toolkit (short) - Sorting, Searching, Linked List, Stack, Queue, BST, Graph
   Compile: gcc dsa_toolkit_short.c -o dsa      Run: ./dsa */
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX 100

int in(const char *p) { char b[64]; printf("%s", p); if (!fgets(b, 64, stdin)) exit(0); return atoi(b); }
void swp(int *a, int *b) { int t = *a; *a = *b; *b = t; }
void show(int a[], int n) { for (int i = 0; i < n; i++) printf("%d ", a[i]); puts(""); }

/* ---------- 1. Sorting & Searching ---------- */
void bubble(int a[], int n) { for (int i = 0; i < n - 1; i++) for (int j = 0; j < n - i - 1; j++) if (a[j] > a[j + 1]) swp(&a[j], &a[j + 1]); }
void selection(int a[], int n) { for (int i = 0; i < n - 1; i++) { int m = i; for (int j = i + 1; j < n; j++) if (a[j] < a[m]) m = j; swp(&a[i], &a[m]); } }
void insertion(int a[], int n) { for (int i = 1; i < n; i++) { int k = a[i], j = i - 1; while (j >= 0 && a[j] > k) { a[j + 1] = a[j]; j--; } a[j + 1] = k; } }
void mergeSort(int a[], int l, int r) {
    if (l >= r) return;
    int m = (l + r) / 2, t[MAX], i = l, j = m + 1, k = 0;
    mergeSort(a, l, m); mergeSort(a, m + 1, r);
    while (i <= m && j <= r) t[k++] = a[i] <= a[j] ? a[i++] : a[j++];
    while (i <= m) t[k++] = a[i++];
    while (j <= r) t[k++] = a[j++];
    for (i = 0; i < k; i++) a[l + i] = t[i];
}
void quick(int a[], int lo, int hi) {
    if (lo >= hi) return;
    int p = a[hi], i = lo - 1;
    for (int j = lo; j < hi; j++) if (a[j] < p) swp(&a[++i], &a[j]);
    swp(&a[i + 1], &a[hi]);
    quick(a, lo, i); quick(a, i + 2, hi);
}
int linear(int a[], int n, int k) { for (int i = 0; i < n; i++) if (a[i] == k) return i; return -1; }
int binary(int a[], int n, int k) {
    int lo = 0, hi = n - 1;
    while (lo <= hi) { int m = (lo + hi) / 2; if (a[m] == k) return m; if (a[m] < k) lo = m + 1; else hi = m - 1; }
    return -1;
}
void arrayMenu(void) {
    int a[MAX], c, n = in("Elements (1-100): ");
    if (n < 1 || n > MAX) return;
    for (int i = 0; i < n; i++) a[i] = in("Value: ");
    do {
        printf("\nArray: "); show(a, n);
        puts("1.Bubble 2.Selection 3.Insertion 4.Merge 5.Quick 6.Linear Search 7.Binary Search 0.Back");
        c = in("Choice: ");
        if (c == 1) bubble(a, n); else if (c == 2) selection(a, n); else if (c == 3) insertion(a, n);
        else if (c == 4) mergeSort(a, 0, n - 1); else if (c == 5) quick(a, 0, n - 1);
        else if (c == 6 || c == 7) {
            int k = in("Search: ");
            if (c == 7) quick(a, 0, n - 1);                /* binary search needs sorted data */
            int p = (c == 6) ? linear(a, n, k) : binary(a, n, k);
            if (p >= 0) printf("Found at position %d\n", p + 1); else puts("Not found");
        }
    } while (c);
}

/* ---------- 2. Linked List ---------- */
typedef struct LN { int d; struct LN *next; } LN;
void listMenu(void) {
    LN *h = NULL, *t, *p, *n; int c, v;
    do {
        puts("\n1.Insert begin 2.Insert end 3.Delete 4.Reverse 5.Display 0.Back");
        c = in("Choice: ");
        if (c == 1 || c == 2) {
            n = malloc(sizeof(LN)); n->d = in("Value: "); n->next = NULL;
            if (c == 1) { n->next = h; h = n; }
            else if (!h) h = n;
            else { for (t = h; t->next; t = t->next); t->next = n; }
        } else if (c == 3) {
            v = in("Delete value: ");
            for (p = NULL, t = h; t && t->d != v; p = t, t = t->next);
            if (!t) puts("Not found"); else { if (p) p->next = t->next; else h = t->next; free(t); }
        } else if (c == 4) {
            LN *prev = NULL, *nx;
            for (t = h; t; t = nx) { nx = t->next; t->next = prev; prev = t; }
            h = prev;
        } else if (c == 5) { for (t = h; t; t = t->next) printf("%d -> ", t->d); puts("NULL"); }
    } while (c);
    while (h) { t = h; h = h->next; free(t); }
}

/* ---------- 3. Stack (+ balanced brackets) ---------- */
void stackMenu(void) {
    int s[MAX], top = -1, c; char e[MAX];
    do {
        puts("\n1.Push 2.Pop 3.Display 4.Check brackets 0.Back");
        c = in("Choice: ");
        if (c == 1) { if (top == MAX - 1) puts("Overflow"); else s[++top] = in("Value: "); }
        else if (c == 2) { if (top < 0) puts("Underflow"); else printf("Popped %d\n", s[top--]); }
        else if (c == 3) { for (int i = top; i >= 0; i--) printf("| %d |\n", s[i]); }
        else if (c == 4) {
            printf("Expression: ");
            if (!fgets(e, MAX, stdin)) exit(0);
            e[strcspn(e, "\n")] = 0;
            char st[MAX]; int t = -1, ok = 1;
            for (char *q = e; *q && ok; q++) {
                if (strchr("([{", *q)) st[++t] = *q;
                else if (strchr(")]}", *q)) { if (t < 0 || st[t--] != "([{"[strchr(")]}", *q) - ")]}"]) ok = 0; }
            }
            puts(ok && t < 0 ? "Balanced" : "Not balanced");
        }
    } while (c);
}

/* ---------- 4. Circular Queue ---------- */
void queueMenu(void) {
    int q[5], f = 0, n = 0, c, v;
    do {
        puts("\n1.Enqueue 2.Dequeue 3.Display 0.Back");
        c = in("Choice: ");
        if (c == 1) { if (n == 5) puts("Full"); else { v = in("Value: "); q[(f + n) % 5] = v; n++; } }
        else if (c == 2) { if (!n) puts("Empty"); else { printf("Removed %d\n", q[f]); f = (f + 1) % 5; n--; } }
        else if (c == 3) { for (int i = 0; i < n; i++) printf("%d ", q[(f + i) % 5]); puts(""); }
    } while (c);
}

/* ---------- 5. Binary Search Tree ---------- */
typedef struct TN { int d; struct TN *l, *r; } TN;
TN *ins(TN *t, int v) {
    if (!t) { t = calloc(1, sizeof(TN)); t->d = v; }
    else if (v < t->d) t->l = ins(t->l, v);
    else if (v > t->d) t->r = ins(t->r, v);
    return t;
}
void trav(TN *t, int m) {              /* m: 0 inorder, 1 preorder, 2 postorder */
    if (!t) return;
    if (m == 1) printf("%d ", t->d);
    trav(t->l, m);
    if (m == 0) printf("%d ", t->d);
    trav(t->r, m);
    if (m == 2) printf("%d ", t->d);
}
int ht(TN *t) { if (!t) return 0; int a = ht(t->l), b = ht(t->r); return 1 + (a > b ? a : b); }
int find(TN *t, int v) { while (t && t->d != v) t = v < t->d ? t->l : t->r; return t != NULL; }
void fr(TN *t) { if (t) { fr(t->l); fr(t->r); free(t); } }
void bstMenu(void) {
    TN *root = NULL; int c;
    do {
        puts("\n1.Insert 2.Search 3.Inorder 4.Preorder 5.Postorder 6.Height 0.Back");
        c = in("Choice: ");
        if (c == 1) root = ins(root, in("Value: "));
        else if (c == 2) puts(find(root, in("Search: ")) ? "Found" : "Not found");
        else if (c >= 3 && c <= 5) { trav(root, c - 3); puts(""); }
        else if (c == 6) printf("Height = %d\n", ht(root));
    } while (c);
    fr(root);
}

/* ---------- 6. Graph (BFS / DFS) ---------- */
int g[10][10], V, vis[10];
void dfs(int u) { vis[u] = 1; printf("%d ", u); for (int v = 0; v < V; v++) if (g[u][v] && !vis[v]) dfs(v); }
void bfs(int s) {
    int q[10], f = 0, r = 0;
    vis[s] = 1; q[r++] = s;
    while (f < r) { int u = q[f++]; printf("%d ", u); for (int v = 0; v < V; v++) if (g[u][v] && !vis[v]) { vis[v] = 1; q[r++] = v; } }
}
void graphMenu(void) {
    V = in("Vertices (1-10): ");
    if (V < 1 || V > 10) return;
    memset(g, 0, sizeof g);
    int c;
    do {
        puts("\n1.Add edge 2.BFS 3.DFS 0.Back");
        c = in("Choice: ");
        if (c == 1) {
            int u = in("From: "), v = in("To: ");
            if (u >= 0 && v >= 0 && u < V && v < V) g[u][v] = g[v][u] = 1; else puts("Invalid vertex");
        } else if (c == 2 || c == 3) {
            int s = in("Start: ");
            if (s < 0 || s >= V) { puts("Invalid vertex"); continue; }
            memset(vis, 0, sizeof vis);
            if (c == 2) bfs(s); else dfs(s);
            puts("");
        }
    } while (c);
}

/* ---------- Main ---------- */
int main(void) {
    int c;
    do {
        puts("\n===== DSA TOOLKIT =====\n1.Sorting & Searching 2.Linked List 3.Stack 4.Queue 5.BST 6.Graph 0.Exit");
        c = in("Choice: ");
        if (c == 1) arrayMenu(); else if (c == 2) listMenu(); else if (c == 3) stackMenu();
        else if (c == 4) queueMenu(); else if (c == 5) bstMenu(); else if (c == 6) graphMenu();
    } while (c);
    return 0;
}

<!-- #region editable=true slideshow={"slide_type": ""} -->
## Лабораторная работа № 8 (Binary Search Tree)
***Выполнил***: Зейналли С.Р,  ***Группа***: ИУ10-36
<!-- #endregion -->

### **Цель работы**
Изучение структуры данных «Двоичное дерево поиска», а также основных операций над ним.

### **Задание №1**
**1.** Реализовать программу, выполняющую стандартный набор операций над  двоичным деревом поиска:
- формирование бинарного дерева;
- обход (прямой, симметричный, обратный) бинарного дерева;
- удаление заданной вершины из бинарного дерева;
- поиск заданной вершины в бинарном дереве (по значению);
- печать бинарного дерева на экран;
- проверка пустоты бинарного дерева;
- определение высоты бинарного дерева.


```python
class TreeNode:
    def __init__(self, key):
        self.left = None
        self.right = None
        self.val = key

class BinarySearchTree:
    def __init__(self):
        self.root = None

    def is_empty(self):
        return self.root is None

    def insert(self, key):
        if self.search(key):
            print(f"Key {key} already exists. Insertion skipped.")
            return
        if self.root is None:
            self.root = TreeNode(key)
        else:
            self._insert_rec(self.root, key)

    def _insert_rec(self, node, key):
        if key < node.val:
            if node.left is None:
                node.left = TreeNode(key)
            else:
                self._insert_rec(node.left, key)
        else:
            if node.right is None:
                node.right = TreeNode(key)
            else:
                self._insert_rec(node.right, key)

    def search(self, key):
        return self._search_rec(self.root, key)

    def _search_rec(self, node, key):
        if node is None or node.val == key:
            return node
        if key < node.val:
            return self._search_rec(node.left, key)
        return self._search_rec(node.right, key)

    def delete(self, key):
        if self.search(key) is None:
            print(f"Key {key} not found. Deletion skipped.")
            return
        self.root = self._delete_rec(self.root, key)

    def _delete_rec(self, node, key):
        if node is None:
            return node

        if key < node.val:
            node.left = self._delete_rec(node.left, key)
        elif key > node.val:
            node.right = self._delete_rec(node.right, key)
        else:
            # Node with only one child or no child
            if node.left is None:
                return node.right
            elif node.right is None:
                return node.left

            # Node with two children: get the inorder successor (smallest in the right subtree)
            min_larger_node = self._min_value_node(node.right)
            node.val = min_larger_node.val
            node.right = self._delete_rec(node.right, min_larger_node.val)

        return node

    def _min_value_node(self, node):
        current = node
        while current.left is not None:
            current = current.left
        return current

    def height(self):
        return self._height_rec(self.root)

    def _height_rec(self, node):
        if node is None:
            return -1  
        return 1 + max(self._height_rec(node.left), self._height_rec(node.right))

    def inorder(self):
        return self._inorder_rec(self.root)

    def _inorder_rec(self, node):
        return self._inorder_rec(node.left) + [node.val] + self._inorder_rec(node.right) if node else []

    def preorder(self):
        return self._preorder_rec(self.root)

    def _preorder_rec(self, node):
        return [node.val] + self._preorder_rec(node.left) + self._preorder_rec(node.right) if node else []

    def postorder(self):
        return self._postorder_rec(self.root)

    def _postorder_rec(self, node):
        return self._postorder_rec(node.left) + self._postorder_rec(node.right) + [node.val] if node else []
    
    def print_tree(self):
        if self.is_empty():
            print("Дерево пустое.")
            return
        height = self.height()
        self._print_tree_rec(self.root, 0, height)

    def _print_tree_rec(self, node, current_level, height):
        if node is not None:
            # Сначала печатаем правое поддерево
            self._print_tree_rec(node.right, current_level + 1, height)

            # Печатаем текущий узел
            print("     " * current_level + "  ┌── " + str(node.val))

            # Печатаем левое поддерево
            self._print_tree_rec(node.left, current_level + 1, height)
            
# Пример использования
if __name__ == "__main__":
    bst = BinarySearchTree()
    bst.insert(10)
    bst.insert(5)
    bst.insert(15)
    bst.insert(3)
    bst.insert(7)
    
    print("Inorder traversal:", bst.inorder())
    print("Preorder traversal:", bst.preorder())
    print("Postorder traversal:", bst.postorder())
    print("Height of tree:", bst.height())

    bst.delete(5)
    print("Inorder traversal after deleting 5:", bst.inorder())

    print("Height of tree after deletion:", bst.height())
    print("Searching for 15:", bst.search(15) is not None)
    print("Searching for 99:", bst.search(99) is not None)

    print("Is the tree empty?", bst.is_empty())
    bst.print_tree()

```

    Inorder traversal: [3, 5, 7, 10, 15]
    Preorder traversal: [10, 5, 3, 7, 15]
    Postorder traversal: [3, 7, 5, 15, 10]
    Height of tree: 2
    Inorder traversal after deleting 5: [3, 7, 10, 15]
    Height of tree after deletion: 2
    Searching for 15: True
    Searching for 99: False
    Is the tree empty? False
           ┌── 15
      ┌── 10
           ┌── 7
                ┌── 3


### **Задание №2**
**2.** Реализовать самобалансирующееся дерево (AVL-дерево для четных вариантов, красно-черное дерево для нечетных вариантов)


```python
class Node:
    def __init__(self, key, color, left=None, right=None, parent=None):
        self.key = key
        self.color = color  # 'RED' или 'BLACK'
        self.left = left
        self.right = right
        self.parent = parent

class RedBlackTree:
    def __init__(self):
        self.TNULL = Node(None, 'BLACK')
        self.root = self.TNULL

    def _log(self, message):
        print(message)

    def search(self, key):
        self._log(f"Поиск ключа {key}")
        return self._search_tree_helper(self.root, key)

    def _search_tree_helper(self, node, key):
        if node == self.TNULL or key == node.key:
            return node

        if key < node.key:
            return self._search_tree_helper(node.left, key)
        return self._search_tree_helper(node.right, key)

    def insert(self, key):
        self._log(f"Вставка ключа {key}")
        node = Node(key, 'RED', self.TNULL, self.TNULL, None)
        parent = None
        current = self.root

        while current != self.TNULL:
            parent = current
            if node.key < current.key:
                current = current.left
            else:
                current = current.right

        node.parent = parent
        if parent is None:
            self.root = node
        elif node.key < parent.key:
            parent.left = node
        else:
            parent.right = node

        if node.parent is None:
            node.color = 'BLACK'
            return

        if node.parent.parent is None:
            return

        self._fix_insert(node)

    def _fix_insert(self, node):
        self._log(f"Фиксация вставки для узла с ключом {node.key}")
        while node.parent and node.parent.color == 'RED':
            if node.parent == node.parent.parent.right:
                uncle = node.parent.parent.left
                if uncle.color == 'RED':
                    self._log("Случай 1: Дядя красный")
                    uncle.color = 'BLACK'
                    node.parent.color = 'BLACK'
                    node.parent.parent.color = 'RED'
                    node = node.parent.parent
                else:
                    if node == node.parent.left:
                        self._log("Случай 2: Узел слева")
                        node = node.parent
                        self._right_rotate(node)
                    self._log("Случай 3: Узел справа")
                    node.parent.color = 'BLACK'
                    node.parent.parent.color = 'RED'
                    self._left_rotate(node.parent.parent)
            else:
                uncle = node.parent.parent.right
                if uncle.color == 'RED':
                    self._log("Случай 1: Дядя красный")
                    uncle.color = 'BLACK'
                    node.parent.color = 'BLACK'
                    node.parent.parent.color = 'RED'
                    node = node.parent.parent
                else:
                    if node == node.parent.right:
                        self._log("Случай 2: Узел справа")
                        node = node.parent
                        self._left_rotate(node)
                    self._log("Случай 3: Узел слева")
                    node.parent.color = 'BLACK'
                    node.parent.parent.color = 'RED'
                    self._right_rotate(node.parent.parent)
            if node == self.root:
                break
        self.root.color = 'BLACK'

    def _left_rotate(self, x):
        self._log(f"Левый поворот вокруг узла с ключом {x.key}")
        y = x.right
        x.right = y.left
        if y.left != self.TNULL:
            y.left.parent = x

        y.parent = x.parent
        if x.parent is None:
            self.root = y
        elif x == x.parent.left:
            x.parent.left = y
        else:
            x.parent.right = y

        y.left = x
        x.parent = y

    def _right_rotate(self, x):
        self._log(f"Правый поворот вокруг узла с ключом {x.key}")
        y = x.left
        x.left = y.right
        if y.right != self.TNULL:
            y.right.parent = x

        y.parent = x.parent
        if x.parent is None:
            self.root = y
        elif x == x.parent.right:
            x.parent.right = y
        else:
            x.parent.left = y

        y.right = x
        x.parent = y

    def print_tree(self):
        self._log("Вывод дерева")
        self._print_helper(self.root, "", True)

    def _print_helper(self, node, indent, last):
        if node != self.TNULL:
            print(indent, end="")
            if last:
                print("R----", end="")
                indent += "     "
            else:
                print("L----", end="")
                indent += "|    "

            color = "RED" if node.color == 'RED' else "BLACK"
            print(f"{node.key}({color})")
            self._print_helper(node.left, indent, False)
            self._print_helper(node.right, indent, True)

# Пример использования
if __name__ == "__main__":
    tree = RedBlackTree()
    values = [20, 15, 25, 10, 5, 1]

    for value in values:
        tree.insert(value)

    tree.print_tree()

```

    Вставка ключа 20
    Вставка ключа 15
    Вставка ключа 25
    Вставка ключа 10
    Фиксация вставки для узла с ключом 10
    Случай 1: Дядя красный
    Вставка ключа 5
    Фиксация вставки для узла с ключом 5
    Случай 3: Узел слева
    Правый поворот вокруг узла с ключом 15
    Вставка ключа 1
    Фиксация вставки для узла с ключом 1
    Случай 1: Дядя красный
    Вывод дерева
    R----20(BLACK)
         L----10(RED)
         |    L----5(BLACK)
         |    |    L----1(RED)
         |    R----15(BLACK)
         R----25(BLACK)


### **Индивидуальное задание**
Написать функцию, определяющую количество отрицательных элементов бинарного дерева.


```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None  # Левый дочерний узел
        self.right = None  # Правый дочерний узел

def count_negatives(root):
    if root is None:
        return 0
    # Рекурсивно подсчитываем количество отрицательных узлов в левом и правом поддеревьях
    count = 1 if root.value < 0 else 0
    count += count_negatives(root.left)
    count += count_negatives(root.right)
    return count

# Пример использования:
if __name__ == "__main__":
    # Создание бинарного дерева
    root = TreeNode(10)
    root.left = TreeNode(-5)
    root.right = TreeNode(3)
    root.left.left = TreeNode(-2)
    root.left.right = TreeNode(1)
    root.right.left = TreeNode(-4)
    root.right.right = TreeNode(6)
    root.right.left.left = TreeNode(-7)

    # Подсчет количества отрицательных элементов
    negative_count = count_negatives(root)
    print(f"Количество отрицательных элементов в бинарном дереве: {negative_count}")

```

    Количество отрицательных элементов в бинарном дереве: 4


### **Контрольные вопросы**

1. **С чем связана популярность использования деревьев в программировании?**  
   Деревья популярны из-за их эффективности в организациях данных. Они обеспечивают быстрый доступ, вставку, удаление и поиск элементов, что делает их полезными для баз данных, системных приложений, графов и индексов.

2. **Можно ли список отнести к деревьям? Ответ обоснуйте.**  
   Список не является деревом, так как он имеет линейную структуру, где каждый элемент связан только с одним соседом. Дерево — это иерархическая структура, где у одного узла может быть несколько потомков.

3. **Какие данные содержат адресные поля элемента бинарного дерева?**  
   Адресные поля содержат ссылки на левого и правого потомка, а также на родительский узел (в некоторых реализациях).

4. **Что такое дерево, двоичное дерево, поддерево?**  
   - **Дерево** — иерархическая структура данных с корнем и узлами, связанными ребрами.  
   - **Двоичное дерево** — дерево, где каждый узел имеет не более двух потомков.  
   - **Поддерево** — часть дерева, представляющая собой самостоятельное дерево, начиная с определенного узла.

5. **Как рекурсивно определяется дерево?**  
   Дерево состоит из корня и набора поддеревьев, которые также являются деревьями. Базовый случай: пустое дерево.

6. **Какие основные понятия связываются с деревьями?**  
   Корень, узел, лист, высота, глубина, поддерево, родители, потомки, степень узла.

7. **Какие основные операции характерны при использовании деревьев?**  
   Обход (прямой, симметричный, обратный), поиск, добавление, удаление, балансировка.

8. **Как программно реализуется алгоритм операции обхода дерева?**  
   Обход дерева реализуется с помощью рекурсивных функций или стека. Например:  
   ```python
   def inorder_traversal(node):
       if node:
           inorder_traversal(node.left)
           print(node.key)
           inorder_traversal(node.right)
   ```

9. **Как программно реализуется алгоритм операции добавления элемента в дерево?**  
   Например, для бинарного дерева поиска:  
   ```python
   def insert(node, key):
       if not node:
           return Node(key)
       if key < node.key:
           node.left = insert(node.left, key)
       else:
           node.right = insert(node.right, key)
       return node
   ```

10. **Как программно реализуется алгоритм операции удаления элемента из дерева?**  
   В случае бинарного дерева:  
      * Найти узел.  
      * Если у узла 0 или 1 потомок — удалить и заменить.  
      * Если 2 потомка — найти преемника (минимальный элемент правого поддерева), заменить и удалить.  

   Пример:  
   ```python
   def delete(node, key):
       if not node:
           return node
       if key < node.key:
           node.left = delete(node.left, key)
       elif key > node.key:
           node.right = delete(node.right, key)
       else:
           if not node.left:
               return node.right
           if not node.right:
               return node.left
           temp = find_min(node.right)
           node.key = temp.key
           node.right = delete(node.right, temp.key)
       return node
   ```

11. **Как программно реализуется алгоритм операции поиска элемента в дереве?**  
   Для бинарного дерева поиска:  
   ```python
   def search(node, key):
       if not node or node.key == key:
           return node
       if key < node.key:
           return search(node.left, key)
       return search(node.right, key)
   ```

12. **Что такое самобалансирующееся дерево?**  
   Это дерево, которое автоматически поддерживает свою высоту минимально возможной после операций вставки и удаления, обеспечивая эффективность.

13. **Как определяется количество узлов в самобалансирующемся дереве?**  
   Максимальное количество узлов при высоте `h` соответствует \(2^h - 1\), минимальное — для конкретной структуры (например, AVL или красно-черных деревьев).

14. **Как происходит добавление элемента в самобалансирующееся дерево?**  
   Добавление выполняется аналогично бинарному дереву, но с последующей балансировкой (например, с помощью поворотов или перекраски).

15. **Как происходит удаление элемента из самобалансирующееся дерево?**  
   Удаление включает поиск элемента, удаление по правилам бинарного дерева и последующую балансировку.

16. **Особенности красно-черных деревьев.**  
   - Каждый узел окрашен в красный или черный цвет.  
   - Корень всегда черный.  
   - Красный узел не может иметь красных потомков.  
   - Все пути от узла до листа содержат одинаковое количество черных узлов.  

17. **Особенности АВЛ деревьев.**  
   - Высота левого и правого поддеревьев для любого узла отличается не более чем на 1.  
   - Обеспечивается балансировка после вставки или удаления.  
   - Подходит для частого поиска.

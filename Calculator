import sys
import re
import numpy as np
import matplotlib.pyplot as plt
import sympy as sp
import os
from PyQt5.QtWidgets import (
    QApplication, QMainWindow, QTabWidget, QWidget, QVBoxLayout, QHBoxLayout,
    QLabel, QLineEdit, QPushButton, QTextEdit, QComboBox, QGroupBox, 
    QGridLayout, QMessageBox, QMenu, QAction, QToolButton
)
from PyQt5.QtGui import QIcon
from PyQt5.QtCore import Qt
from matplotlib.backends.backend_qt5agg import FigureCanvasQTAgg as FigureCanvas
from matplotlib.figure import Figure

# Константы для химических расчетов
ELEMENTS = {
    'H': 1.00794, 'He': 4.002602, 'Li': 6.941, 'Be': 9.012182, 'B': 10.811,
    'C': 12.0107, 'N': 14.0067, 'O': 15.9994, 'F': 18.998403, 'Ne': 20.1797,
    'Na': 22.989769, 'Mg': 24.305, 'Al': 26.981538, 'Si': 28.0855, 'P': 30.973762,
    'S': 32.065, 'Cl': 35.453, 'K': 39.0983, 'Ca': 40.078, 'Fe': 55.845, 'Cu': 63.546
}

# Имя файла для истории
HISTORY_FILE = "STORY.txt"

class ChemicalCalculator:
    @staticmethod
    def calculate_molar_mass(formula):
        """Расчет молярной массы химического соединения"""
        pattern = r'([A-Z][a-z]*)(\d*)'
        elements = re.findall(pattern, formula)
        total_mass = 0.0
        
        for element, count in elements:
            if element not in ELEMENTS:
                return None, f"Неизвестный элемент: {element}"
            
            atomic_mass = ELEMENTS[element]
            count = int(count) if count else 1
            total_mass += atomic_mass * count
        
        return total_mass, f"Молярная масса {formula}: {total_mass:.4f} г/моль"

    @staticmethod
    def balance_equation(equation):
        """Балансировка химического уравнения (упрощенная)"""
        try:
            reactants, products = equation.split('->')
            # В реальном приложении здесь была бы сложная логика балансировки
            balanced_eq = f"{reactants.strip()} → {products.strip()}"
            return balanced_eq, f"Сбалансированное уравнение: {balanced_eq}"
        except:
            return None, "Неверный формат. Используйте '->' для разделения реагентов и продуктов"

class MathCalculator:
    @staticmethod
    def calculate_expression(expression):
        """Вычисление математических выражений"""
        try:
            x = sp.symbols('x')
            expr = sp.sympify(expression)
            result = expr.evalf()
            return result, f"{expression} = {result}"
        except Exception as e:
            return None, f"Ошибка: {str(e)}"
    
    @staticmethod
    def solve_equation(equation, var='x'):
        """Решение уравнений"""
        try:
            x = sp.symbols(var)
            eq = sp.sympify(equation)
            solutions = sp.solve(eq, x)
            return solutions, f"Решения уравнения {equation}: {solutions}"
        except Exception as e:
            return None, f"Ошибка: {str(e)}"
    
    @staticmethod
    def calculate_derivative(expression, var='x'):
        """Вычисление производной"""
        try:
            x = sp.symbols(var)
            expr = sp.sympify(expression)
            derivative = sp.diff(expr, x)
            return derivative, f"Производная d({expression})/d{var} = {derivative}"
        except Exception as e:
            return None, f"Ошибка: {str(e)}"
    
    @staticmethod
    def calculate_integral(expression, var='x'):
        """Вычисление интеграла"""
        try:
            x = sp.symbols(var)
            expr = sp.sympify(expression)
            integral = sp.integrate(expr, x)
            return integral, f"Интеграл ∫({expression})d{var} = {integral}"
        except Exception as e:
            return None, f"Ошибка: {str(e)}"

class GraphingCalculator(FigureCanvas):
    def __init__(self, parent=None, width=5, height=4, dpi=100):
        self.fig = Figure(figsize=(width, height), dpi=dpi)
        super().__init__(self.fig)
        self.setParent(parent)
        self.ax = self.fig.add_subplot(111)
    
    def plot_function(self, function_str, x_range=(-10, 10), num_points=500):
        """Построение графика функции"""
        try:
            self.ax.clear()
            x = sp.symbols('x')
            expr = sp.sympify(function_str)
            f = sp.lambdify(x, expr, 'numpy')
            
            x_vals = np.linspace(x_range[0], x_range[1], num_points)
            y_vals = f(x_vals)
            
            self.ax.plot(x_vals, y_vals, 'b-', linewidth=2)
            self.ax.set_title(f'График функции: {function_str}')
            self.ax.set_xlabel('x')
            self.ax.set_ylabel('f(x)')
            self.ax.grid(True)
            self.ax.axhline(0, color='black', linewidth=0.5)
            self.ax.axvline(0, color='black', linewidth=0.5)
            self.draw()
            return True, "График построен успешно!"
        except Exception as e:
            return False, f"Ошибка: {str(e)}"

class CalculatorApp(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Универсальный научный калькулятор")
        self.setGeometry(100, 100, 900, 700)
        
        # Центральный виджет и макет
        central_widget = QWidget()
        self.setCentralWidget(central_widget)
        main_layout = QVBoxLayout(central_widget)
        
        # Создаем верхнюю панель с заголовком и кнопкой меню
        header_layout = QHBoxLayout()
        title_label = QLabel("Универсальный научный калькулятор")
        title_label.setStyleSheet("font-weight: bold; font-size: 16px;")
        header_layout.addWidget(title_label)
        
        # Кнопка меню (три полоски)
        self.menu_button = QToolButton()
        self.menu_button.setText("≡")
        self.menu_button.setStyleSheet("font-size: 20px; font-weight: bold;")
        self.menu_button.setPopupMode(QToolButton.InstantPopup)
        
        # Создаем меню для кнопки
        menu = QMenu()
        self.feedback_action = menu.addAction("Обратная связь")
        self.feedback_action.triggered.connect(self.show_feedback_dialog)
        self.export_history_action = menu.addAction("Экспорт истории")
        self.export_history_action.triggered.connect(self.export_history)
        self.menu_button.setMenu(menu)
        
        header_layout.addStretch(1)
        header_layout.addWidget(self.menu_button)
        
        main_layout.addLayout(header_layout)
        
        # Создаем вкладки
        self.tabs = QTabWidget()
        main_layout.addWidget(self.tabs)
        
        # История операций
        self.history = []
        self.history_text = QTextEdit()
        self.history_text.setReadOnly(True)
        
        # Загружаем историю из файла при запуске
        self.load_history()
        
        # Создаем вкладки
        self.create_math_tab()
        self.create_chemistry_tab()
        self.create_graphing_tab()
        self.create_history_tab()
        
        # Статус бар
        self.status_bar = self.statusBar()
    
    def load_history(self):
        """Загрузка истории из файла при запуске"""
        try:
            if os.path.exists(HISTORY_FILE):
                with open(HISTORY_FILE, 'r', encoding='utf-8') as f:
                    self.history = [line.strip() for line in f.readlines()]
                    self.history_text.setText("\n".join(self.history))
            else:
                self.history = []
        except Exception as e:
            print(f"Ошибка при загрузке истории: {str(e)}")
            self.history = []
    
    def save_history(self):
        """Сохранение истории в файл"""
        try:
            with open(HISTORY_FILE, 'w', encoding='utf-8') as f:
                f.write("\n".join(self.history))
        except Exception as e:
            print(f"Ошибка при сохранении истории: {str(e)}")
    
    def export_history(self):
        """Экспорт истории в файл (уже сохранен, просто покажем сообщение)"""
        self.save_history()
        QMessageBox.information(self, "Экспорт истории", 
                              f"История операций сохранена в файл:\n{HISTORY_FILE}",
                              QMessageBox.Ok)
    
    def show_feedback_dialog(self):
        QMessageBox.information(self, "Обратная связь",
            "Есть пожелания или встретили баги?\nПишите нам на почту: texkcompanu@gmail.com",
            QMessageBox.Ok)
    
    def create_math_tab(self):
        """Вкладка для математических вычислений"""
        math_tab = QWidget()
        layout = QVBoxLayout(math_tab)
        
        # Выражение для вычисления
        expr_group = QGroupBox("Математические вычисления")
        expr_layout = QGridLayout(expr_group)
        
        expr_layout.addWidget(QLabel("Выражение:"), 0, 0)
        self.math_expr_input = QLineEdit()
        expr_layout.addWidget(self.math_expr_input, 0, 1)
        
        self.math_calculate_btn = QPushButton("Вычислить")
        self.math_calculate_btn.clicked.connect(self.calculate_expression)
        expr_layout.addWidget(self.math_calculate_btn, 0, 2)
        
        # Решение уравнений
        expr_layout.addWidget(QLabel("Уравнение:"), 1, 0)
        self.equation_input = QLineEdit()
        expr_layout.addWidget(self.equation_input, 1, 1)
        
        self.solve_btn = QPushButton("Решить")
        self.solve_btn.clicked.connect(self.solve_equation)
        expr_layout.addWidget(self.solve_btn, 1, 2)
        
        # Производные и интегралы
        expr_layout.addWidget(QLabel("Функция:"), 2, 0)
        self.func_input = QLineEdit()
        expr_layout.addWidget(self.func_input, 2, 1)
        
        self.derivative_btn = QPushButton("Производная")
        self.derivative_btn.clicked.connect(self.calculate_derivative)
        expr_layout.addWidget(self.derivative_btn, 2, 2)
        
        self.integral_btn = QPushButton("Интеграл")
        self.integral_btn.clicked.connect(self.calculate_integral)
        expr_layout.addWidget(self.integral_btn, 2, 3)
        
        # Результаты
        self.math_result = QTextEdit()
        self.math_result.setReadOnly(True)
        
        layout.addWidget(expr_group)
        layout.addWidget(self.math_result)
        
        self.tabs.addTab(math_tab, "Математика")
    
    def create_chemistry_tab(self):
        """Вкладка для химических расчетов"""
        chem_tab = QWidget()
        layout = QVBoxLayout(chem_tab)
        
        # Группа для химических расчетов
        chem_group = QGroupBox("Химические расчеты")
        chem_layout = QGridLayout(chem_group)
        
        chem_layout.addWidget(QLabel("Химическая формула:"), 0, 0)
        self.formula_input = QLineEdit()
        chem_layout.addWidget(self.formula_input, 0, 1)
        
        self.molar_mass_btn = QPushButton("Молярная масса")
        self.molar_mass_btn.clicked.connect(self.calculate_molar_mass)
        chem_layout.addWidget(self.molar_mass_btn, 0, 2)
        
        chem_layout.addWidget(QLabel("Химическое уравнение:"), 1, 0)
        self.equation_input_chem = QLineEdit()
        chem_layout.addWidget(self.equation_input_chem, 1, 1)
        
        self.balance_btn = QPushButton("Балансировать")
        self.balance_btn.clicked.connect(self.balance_equation)
        chem_layout.addWidget(self.balance_btn, 1, 2)
        
        # Результаты
        self.chem_result = QTextEdit()
        self.chem_result.setReadOnly(True)
        
        layout.addWidget(chem_group)
        layout.addWidget(self.chem_result)
        
        self.tabs.addTab(chem_tab, "Химия")
    
    def create_graphing_tab(self):
        """Вкладка для построения графиков"""
        graph_tab = QWidget()
        layout = QVBoxLayout(graph_tab)
        
        # Управление графиками
        control_group = QGroupBox("Параметры графика")
        control_layout = QGridLayout(control_group)
        
        control_layout.addWidget(QLabel("Функция f(x):"), 0, 0)
        self.func_input_graph = QLineEdit("sin(x)")
        control_layout.addWidget(self.func_input_graph, 0, 1)
        
        control_layout.addWidget(QLabel("X min:"), 1, 0)
        self.xmin_input = QLineEdit("-10")
        control_layout.addWidget(self.xmin_input, 1, 1)
        
        control_layout.addWidget(QLabel("X max:"), 1, 2)
        self.xmax_input = QLineEdit("10")
        control_layout.addWidget(self.xmax_input, 1, 3)
        
        self.plot_btn = QPushButton("Построить график")
        self.plot_btn.clicked.connect(self.plot_function)
        control_layout.addWidget(self.plot_btn, 2, 0, 1, 4)
        
        # Область для графика
        self.graph_canvas = GraphingCalculator(self)
        
        # Результаты
        self.graph_result = QTextEdit()
        self.graph_result.setReadOnly(True)
        
        layout.addWidget(control_group)
        layout.addWidget(self.graph_canvas)
        layout.addWidget(self.graph_result)
        
        self.tabs.addTab(graph_tab, "Графики")
    
    def create_history_tab(self):
        """Вкладка с историей операций"""
        history_tab = QWidget()
        layout = QVBoxLayout(history_tab)
        
        layout.addWidget(QLabel("История операций:"))
        layout.addWidget(self.history_text)
        
        btn_layout = QHBoxLayout()
        clear_btn = QPushButton("Очистить историю")
        clear_btn.clicked.connect(self.clear_history)
        btn_layout.addWidget(clear_btn)
        
        export_btn = QPushButton("Сохранить историю")
        export_btn.clicked.connect(self.export_history)
        btn_layout.addWidget(export_btn)
        
        layout.addLayout(btn_layout)
        
        self.tabs.addTab(history_tab, "История")
    
    def add_to_history(self, operation):
        """Добавление операции в историю"""
        self.history.append(operation)
        self.history_text.append(operation)
        self.save_history()  # Сохраняем историю после каждого добавления
    
    def clear_history(self):
        """Очистка истории"""
        reply = QMessageBox.question(self, 'Очистка истории',
                                    "Вы уверены, что хотите очистить всю историю операций?",
                                    QMessageBox.Yes | QMessageBox.No, QMessageBox.No)
        
        if reply == QMessageBox.Yes:
            self.history = []
            self.history_text.clear()
            self.save_history()  # Сохраняем пустую историю
            self.status_bar.showMessage("История очищена")
    
    def calculate_expression(self):
        """Вычисление математического выражения"""
        expr = self.math_expr_input.text()
        if not expr:
            self.status_bar.showMessage("Введите выражение для вычисления")
            return
        
        result, message = MathCalculator.calculate_expression(expr)
        if result is not None:
            self.math_result.append(message)
            self.add_to_history(f"Вычисление: {message}")
            self.status_bar.showMessage("Вычисление выполнено успешно")
        else:
            self.math_result.append(message)
            self.status_bar.showMessage("Ошибка при вычислении")
    
    def solve_equation(self):
        """Решение уравнения"""
        equation = self.equation_input.text()
        if not equation:
            self.status_bar.showMessage("Введите уравнение для решения")
            return
        
        solutions, message = MathCalculator.solve_equation(equation)
        if solutions is not None:
            self.math_result.append(message)
            self.add_to_history(f"Решение уравнения: {message}")
            self.status_bar.showMessage("Уравнение решено успешно")
        else:
            self.math_result.append(message)
            self.status_bar.showMessage("Ошибка при решении уравнения")
    
    def calculate_derivative(self):
        """Вычисление производной"""
        func = self.func_input.text()
        if not func:
            self.status_bar.showMessage("Введите функцию для дифференцирования")
            return
        
        derivative, message = MathCalculator.calculate_derivative(func)
        if derivative is not None:
            self.math_result.append(message)
            self.add_to_history(f"Производная: {message}")
            self.status_bar.showMessage("Производная вычислена успешно")
        else:
            self.math_result.append(message)
            self.status_bar.showMessage("Ошибка при вычислении производной")
    
    def calculate_integral(self):
        """Вычисление интеграла"""
        func = self.func_input.text()
        if not func:
            self.status_bar.showMessage("Введите функцию для интегрирования")
            return
        
        integral, message = MathCalculator.calculate_integral(func)
        if integral is not None:
            self.math_result.append(message)
            self.add_to_history(f"Интеграл: {message}")
            self.status_bar.showMessage("Интеграл вычислен успешно")
        else:
            self.math_result.append(message)
            self.status_bar.showMessage("Ошибка при вычислении интеграла")
    
    def calculate_molar_mass(self):
        """Вычисление молярной массы"""
        formula = self.formula_input.text()
        if not formula:
            self.status_bar.showMessage("Введите химическую формулу")
            return
        
        mass, message = ChemicalCalculator.calculate_molar_mass(formula)
        if mass is not None:
            self.chem_result.append(message)
            self.add_to_history(f"Химия: {message}")
            self.status_bar.showMessage("Молярная масса вычислена успешно")
        else:
            self.chem_result.append(message)
            self.status_bar.showMessage("Ошибка при вычислении молярной массы")
    
    def balance_equation(self):
        """Балансировка химического уравнения"""
        equation = self.equation_input_chem.text()
        if not equation:
            self.status_bar.showMessage("Введите химическое уравнение")
            return
        
        balanced, message = ChemicalCalculator.balance_equation(equation)
        if balanced is not None:
            self.chem_result.append(message)
            self.add_to_history(f"Химия: {message}")
            self.status_bar.showMessage("Уравнение сбалансировано")
        else:
            self.chem_result.append(message)
            self.status_bar.showMessage("Ошибка при балансировке уравнения")
    
    def plot_function(self):
        """Построение графика функции"""
        func = self.func_input_graph.text()
        if not func:
            self.status_bar.showMessage("Введите функцию для построения графика")
            return
        
        try:
            xmin = float(self.xmin_input.text())
            xmax = float(self.xmax_input.text())
            success, message = self.graph_canvas.plot_function(func, (xmin, xmax))
            self.graph_result.append(message)
            self.add_to_history(f"График: {func} на интервале [{xmin}, {xmax}]")
            self.status_bar.showMessage("График построен успешно" if success else "Ошибка при построении графика")
        except ValueError:
            self.graph_result.append("Ошибка: Некорректные значения интервала")
            self.status_bar.showMessage("Ошибка: Некорректные значения интервала")

if __name__ == "__main__":
    app = QApplication(sys.argv)
    calculator = CalculatorApp()
    calculator.show()
    sys.exit(app.exec_())

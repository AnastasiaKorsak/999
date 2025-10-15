using System;
using System.Drawing;
using System.Windows.Forms;

namespace ExpressionCalculator
{
    public class MainForm : Form
    {
        private TextBox textBoxX;
        private TextBox textBoxY;
        private TextBox textBoxZ;
        private TextBox textBoxResult;
        private Button buttonCalculate;
        private Label labelX;
        private Label labelY;
        private Label labelZ;
        private Label labelResult;

        public MainForm()
        {
            InitializeComponent();
        }

        private void InitializeComponent()
        {
            // Настройка формы
            this.Text = "Калькулятор выражения";
            this.Size = new Size(400, 300);
            this.StartPosition = FormStartPosition.CenterScreen;
            this.Padding = new Padding(20);

            // Создание и настройка элементов управления
            labelX = new Label
            {
                Text = "X:",
                Location = new Point(20, 20),
                Size = new Size(100, 20)
            };

            textBoxX = new TextBox
            {
                Location = new Point(120, 20),
                Size = new Size(200, 20),
                Text = "-4.5"
            };

            labelY = new Label
            {
                Text = "Y:",
                Location = new Point(20, 60),
                Size = new Size(100, 20)
            };

            textBoxY = new TextBox
            {
                Location = new Point(120, 60),
                Size = new Size(200, 20),
                Text = "0.75e-4"
            };

            labelZ = new Label
            {
                Text = "Z:",
                Location = new Point(20, 100),
                Size = new Size(100, 20)
            };

            textBoxZ = new TextBox
            {
                Location = new Point(120, 100),
                Size = new Size(200, 20),
                Text = "0.845e2"
            };

            labelResult = new Label
            {
                Text = "Результат u:",
                Location = new Point(20, 140),
                Size = new Size(100, 20)
            };

            textBoxResult = new TextBox
            {
                Location = new Point(120, 140),
                Size = new Size(200, 20),
                ReadOnly = true
            };

            buttonCalculate = new Button
            {
                Text = "Вычислить",
                Location = new Point(120, 180),
                Size = new Size(100, 30)
            };

            buttonCalculate.Click += ButtonCalculate_Click;

            // Добавление элементов на форму
            this.Controls.Add(labelX);
            this.Controls.Add(textBoxX);
            this.Controls.Add(labelY);
            this.Controls.Add(textBoxY);
            this.Controls.Add(labelZ);
            this.Controls.Add(textBoxZ);
            this.Controls.Add(labelResult);
            this.Controls.Add(textBoxResult);
            this.Controls.Add(buttonCalculate);
        }

        private void ButtonCalculate_Click(object sender, EventArgs e)
        {
            try
            {
                // Парсинг входных значений
                double x = double.Parse(textBoxX.Text);
                double y = double.Parse(textBoxY.Text);
                double z = double.Parse(textBoxZ.Text);

                // Вычисление выражения
                double result = CalculateExpression(x, y, z);

                // Вывод результата
                textBoxResult.Text = result.ToString("F6");
            }
            catch (FormatException)
            {
                MessageBox.Show("Ошибка формата чисел. Проверьте введенные значения.", 
                              "Ошибка", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
            catch (OverflowException)
            {
                MessageBox.Show("Слишком большое или слишком маленькое число.", 
                              "Ошибка", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
            catch (Exception ex)
            {
                MessageBox.Show($"Ошибка при вычислении: {ex.Message}", 
                              "Ошибка", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        private double CalculateExpression(double x, double y, double z)
        {
            // Вычисление |x - y|
            double absDiff = Math.Abs(x - y);
            
            // Вычисление первой части: ∛(8 + |x - y|² + 1)
            double firstPart = Math.Pow(8 + Math.Pow(absDiff, 2) + 1, 1.0 / 3.0);
            
            // Вычисление tg²z + 1
            double tanZ = Math.Tan(z);
            double tanZSquaredPlusOne = Math.Pow(tanZ, 2) + 1;
            
            // Вычисление второй части: e^{|x - y|} * (tg²z + 1)^x
            double secondPart = Math.Exp(absDiff) * Math.Pow(tanZSquaredPlusOne, x);
            
            // Итоговый результат
            double u = firstPart - secondPart;
            
            return u;
        }

        [STAThread]
        static void Main()
        {
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);
            Application.Run(new MainForm());
        }
    }
}

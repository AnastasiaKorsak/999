using System;
using System.Drawing;
using System.Windows.Forms;

namespace ExpressionCalculator
{
    public partial class Form1 : Form
    {
        private TextBox textBoxX;
        private TextBox textBoxY;
        private TextBox textBoxZ;
        private TextBox textBoxResult;
        private Button buttonCalculate;

        public Form1()
        {
            InitializeComponent();
        }

        private void InitializeComponent()
        {
            // Настройка формы
            this.Text = "Калькулятор выражения";
            this.ClientSize = new Size(350, 250);
            this.StartPosition = FormStartPosition.CenterScreen;
            this.FormBorderStyle = FormBorderStyle.FixedDialog;
            this.MaximizeBox = false;

            // Создание и настройка элементов управления
            Label labelX = new Label();
            labelX.Text = "X:";
            labelX.Location = new Point(20, 20);
            labelX.Size = new Size(100, 20);

            textBoxX = new TextBox();
            textBoxX.Location = new Point(120, 20);
            textBoxX.Size = new Size(200, 20);
            textBoxX.Text = "-4,5";

            Label labelY = new Label();
            labelY.Text = "Y:";
            labelY.Location = new Point(20, 50);
            labelY.Size = new Size(100, 20);

            textBoxY = new TextBox();
            textBoxY.Location = new Point(120, 50);
            textBoxY.Size = new Size(200, 20);
            textBoxY.Text = "0,000075";

            Label labelZ = new Label();
            labelZ.Text = "Z:";
            labelZ.Location = new Point(20, 80);
            labelZ.Size = new Size(100, 20);

            textBoxZ = new TextBox();
            textBoxZ.Location = new Point(120, 80);
            textBoxZ.Size = new Size(200, 20);
            textBoxZ.Text = "84,5";

            Label labelResult = new Label();
            labelResult.Text = "Результат u:";
            labelResult.Location = new Point(20, 110);
            labelResult.Size = new Size(100, 20);

            textBoxResult = new TextBox();
            textBoxResult.Location = new Point(120, 110);
            textBoxResult.Size = new Size(200, 20);
            textBoxResult.ReadOnly = true;

            buttonCalculate = new Button();
            buttonCalculate.Text = "Вычислить";
            buttonCalculate.Location = new Point(120, 150);
            buttonCalculate.Size = new Size(100, 30);
            buttonCalculate.Click += new EventHandler(ButtonCalculate_Click);

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
                // Парсинг входных значений (используем запятую как разделитель)
                double x = double.Parse(textBoxX.Text.Replace(".", ","));
                double y = double.Parse(textBoxY.Text.Replace(".", ","));
                double z = double.Parse(textBoxZ.Text.Replace(".", ","));

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
            Application.Run(new Form1());
        }
    }
}

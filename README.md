# Lab-Final-VP
//Xaml code
<Window x:Class="QuizApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Quiz Questions" Height="800" Width="1200" Background="#f0f0f0">
    <Grid Background="#ffffff">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="300" />
        </Grid.ColumnDefinitions>

        <!-- Filter ComboBox -->
        <StackPanel Orientation="Horizontal" Grid.Row="0" Margin="30" Background="#dfe3e6">
            <Label Content="Filter Questions:" VerticalAlignment="Center" FontSize="20" Margin="0,0,20,0" Foreground="#333333"/>
            <ComboBox Name="FilterComboBox" Width="300" Margin="0,0,20,0" FontSize="20" 
                      Background="#ffffff" Foreground="#333333" 
                      SelectionChanged="FilterComboBox_SelectionChanged">
                <ComboBoxItem Content="All" />
                <ComboBoxItem Content="Easy" />
                <ComboBoxItem Content="Medium" />
                <ComboBoxItem Content="Hard" />
            </ComboBox>
        </StackPanel>

        <!-- DataGrid for Quiz Questions -->
        <DataGrid Name="QuizDataGrid" Grid.Row="1" Grid.ColumnSpan="2" Margin="10,30,50,30"
                  AutoGenerateColumns="False" IsReadOnly="True" FontSize="18"
                  Background="#f9f9f9" BorderBrush="#cccccc" BorderThickness="1">
            <DataGrid.Columns>
                <DataGridTextColumn Header="Question Text" Binding="{Binding QuestionText}" Width="2*" />
                <DataGridTextColumn Header="Options" Binding="{Binding Options}" Width="2*" />
                <DataGridTextColumn Header="Correct Answer" Binding="{Binding CorrectAnswer}" Width="1*" />
                <DataGridTextColumn Header="Marks" Binding="{Binding Marks}" Width="1*" />
                <DataGridTextColumn Header="Time Limit" Binding="{Binding TimeLimit}" Width="1*" />
            </DataGrid.Columns>
        </DataGrid>

        <!-- Action Buttons -->
        <StackPanel Orientation="Horizontal" Grid.Row="2" Grid.Column="0" Margin="30">
            <Button Content="Start Quiz" Width="180" Height="50" Margin="15" FontSize="18" 
                    Background="#4CAF50" Foreground="#ffffff" BorderBrush="#388E3C" BorderThickness="2" Click="StartQuizButton_Click" />
            <Button Content="Reset Filters" Width="180" Height="50" Margin="15" FontSize="18" 
                    Background="#F44336" Foreground="#ffffff" BorderBrush="#D32F2F" BorderThickness="2" Click="ResetFiltersButton_Click" />
        </StackPanel>

        <!-- Progress Bar -->
        <ProgressBar Grid.Row="2" Grid.Column="1" Margin="30" Height="30"
                     Name="LoadingProgressBar" Minimum="0" Maximum="100" Value="0" Visibility="Hidden"
                     Background="#E0E0E0" Foreground="#4CAF50"/>
    </Grid>
</Window>

/// Main Windoqw.cs Code
using System;
using System.Collections.Generic;
using System.Data.SqlClient;
using System.Linq;
using System.Windows;

namespace QuizApp
{
    public partial class MainWindow : Window
    {
        // List to hold the questions
        public List<Question> Questions { get; set; }

        // Connection string for SQL Server
        private string connectionString = @"Server=DESKTOP-NNHS9J7\SQLEXPRESS;Database=QuizDatabase;Integrated Security=True;";

        public MainWindow()
        {
            InitializeComponent();
            LoadQuestionsFromDatabase(); // Fetch questions from the database
            QuizDataGrid.ItemsSource = Questions;
        }

        // Fetch questions from the database
        private void LoadQuestionsFromDatabase()
        {
            Questions = new List<Question>();

            // SQL query to get the questions
            string query = "SELECT QuestionText, Options, CorrectAnswer, Marks, TimeLimit, Difficulty FROM Questions";

            // Create a new SQL connection using the connection string
            using (SqlConnection conn = new SqlConnection(connectionString))
            {
                try
                {
                    conn.Open();
                    SqlCommand cmd = new SqlCommand(query, conn);

                    // Execute the command and read the results
                    SqlDataReader reader = cmd.ExecuteReader();

                    while (reader.Read())
                    {
                        Questions.Add(new Question
                        {
                            QuestionText = reader["QuestionText"].ToString(),
                            Options = reader["Options"].ToString(),
                            CorrectAnswer = reader["CorrectAnswer"].ToString(),
                            Marks = Convert.ToInt32(reader["Marks"]),
                            TimeLimit = Convert.ToInt32(reader["TimeLimit"]),
                            Difficulty = reader["Difficulty"].ToString()
                        });
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show("Error while loading data: " + ex.Message);
                }
            }
        }

        // Filter the questions based on selected difficulty
        private void FilterComboBox_SelectionChanged(object sender, System.Windows.Controls.SelectionChangedEventArgs e)
        {
            var selectedDifficulty = ((ComboBoxItem)FilterComboBox.SelectedItem)?.Content.ToString();
            if (selectedDifficulty == "All")
            {
                QuizDataGrid.ItemsSource = Questions;
            }
            else
            {
                QuizDataGrid.ItemsSource = Questions.Where(q => q.Difficulty == selectedDifficulty).ToList();
            }
        }

        // Handle Start Quiz button click
        private void StartQuizButton_Click(object sender, RoutedEventArgs e)
        {
            MessageBox.Show("Starting the quiz...");
            // Implement quiz start logic here
        }

        // Handle Reset Filters button click
        private void ResetFiltersButton_Click(object sender, RoutedEventArgs e)
        {
            FilterComboBox.SelectedIndex = 0; // Reset to "All"
            QuizDataGrid.ItemsSource = Questions;
        }
    }

    // Question model
    public class Question
    {
        public string QuestionText { get; set; }
        public string Options { get; set; }
        public string CorrectAnswer { get; set; }
        public int Marks { get; set; }
        public int TimeLimit { get; set; }
        public string Difficulty { get; set; }
    }
}
private string connectionString = @"Server=DESKTOP-NNHS9J7\SQLEXPRESS;Database=QuizDatabase;Integrated Security=True;";




//// App.Config
<Window x:Class="QuizApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Quiz Questions" Height="800" Width="1200" Background="#f0f0f0">
    <Grid Background="#ffffff">
        <!-- Define Row and Column Definitions for Grid -->
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="300" />
        </Grid.ColumnDefinitions>

        <!-- Filter ComboBox -->
        <StackPanel Orientation="Horizontal" Grid.Row="0" Margin="30" Background="#dfe3e6">
            <Label Content="Filter Questions:" VerticalAlignment="Center" FontSize="20" Margin="0,0,20,0" Foreground="#333333"/>
            <ComboBox Name="FilterComboBox" Width="300" Margin="0,0,20,0" FontSize="20" 
                      Background="#ffffff" Foreground="#333333" 
                      SelectionChanged="FilterComboBox_SelectionChanged">
                <ComboBoxItem Content="All" />
                <ComboBoxItem Content="Easy" />
                <ComboBoxItem Content="Medium" />
                <ComboBoxItem Content="Hard" />
            </ComboBox>
        </StackPanel>

        <!-- DataGrid for Quiz Questions -->
        <DataGrid Name="QuizDataGrid" Grid.Row="1" Grid.ColumnSpan="2" Margin="30"
                  AutoGenerateColumns="False" IsReadOnly="True" HorizontalAlignment="Stretch" VerticalAlignment="Stretch" FontSize="18"
                  Background="#f9f9f9" BorderBrush="#cccccc" BorderThickness="1" ItemsSource="{Binding QuizQuestionsList}">
            <DataGrid.Columns>
                <DataGridTextColumn Header="Question Text" Binding="{Binding QuestionText}" Width="2*" />
                <DataGridTextColumn Header="Options" Binding="{Binding Options}" Width="2*" />
                <DataGridTextColumn Header="Correct Answer" Binding="{Binding CorrectAnswer}" Width="1*" />
                <DataGridTextColumn Header="Marks" Binding="{Binding Marks}" Width="1*" />
                <DataGridTextColumn Header="Time Limit" Binding="{Binding TimeLimit}" Width="1*" />
            </DataGrid.Columns>
        </DataGrid>

        <!-- Action Buttons -->
        <StackPanel Orientation="Horizontal" Grid.Row="2" Grid.Column="0" Margin="30">
            <Button Content="Start Quiz" Width="180" Height="50" Margin="15" FontSize="18" 
                    Background="#4CAF50" Foreground="#ffffff" BorderBrush="#388E3C" BorderThickness="2" Click="StartQuizButton_Click" />
            <Button Content="Reset Filters" Width="180" Height="50" Margin="15" FontSize="18" 
                    Background="#F44336" Foreground="#ffffff" BorderBrush="#D32F2F" BorderThickness="2" Click="ResetFiltersButton_Click" />
        </StackPanel>

        <!-- Progress Bar -->
        <ProgressBar Grid.Row="2" Grid.Column="1" Margin="30" Height="30"
                     Name="LoadingProgressBar" Minimum="0" Maximum="100" Value="0" Visibility="Hidden"
                     Background="#E0E0E0" Foreground="#4CAF50"/>
    </Grid>
</Window>


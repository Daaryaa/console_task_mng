using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using System.IO;
using System.Text.Json;

namespace TaskManagerDemo
{
    // 1. ООП: Класс с авто-свойствами и переопределением метода
    public class TaskItem
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public bool IsCompleted { get; set; }
        public DateTime CreatedAt { get; set; }

        public override string ToString()
        {
            string status = IsCompleted ? "[X]" : "[ ]";
            return $"{status} #{Id} - {Title} (Создана: {CreatedAt:dd.MM.yy})";
        }
    }

    class Program
    {
        // 2. Работа с коллекциями (List) и статический список
        private static List<TaskItem> _tasks = new List<TaskItem>();
        private static int _nextId = 1;

        // 3. Асинхронность (async/await) для имитации загрузки
        static async Task Main(string[] args)
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("=== Менеджер задач (C# Demo) ===");

            // Имитация асинхронной загрузки данных
            await LoadTasksAsync();

            bool exit = false;
            while (!exit)
            {
                ShowMenu();
                var choice = Console.ReadLine();

                // 4. Использование switch expression (C# 8.0+)
                exit = choice?.ToLower() switch
                {
                    "1" => { AddTask(); false; },
                    "2" => { ShowTasks(); false; },
                    "3" => { CompleteTask(); false; },
                    "4" => { DeleteTask(); false; },
                    "5" => { SaveTasks(); false; },
                    "6" => true,
                    _ => { Console.WriteLine("Неверный ввод."); false; }
                };
            }
        }

        static void ShowMenu()
        {
            Console.WriteLine("\nВыберите действие:");
            Console.WriteLine("1. Добавить задачу");
            Console.WriteLine("2. Показать все задачи");
            Console.WriteLine("3. Отметить как выполненную");
            Console.WriteLine("4. Удалить задачу");
            Console.WriteLine("5. Сохранить в файл (JSON)");
            Console.WriteLine("6. Выход");
            Console.Write("> ");
        }

        static void AddTask()
        {
            Console.Write("Введите название задачи: ");
            string title = Console.ReadLine();
            if (string.IsNullOrWhiteSpace(title))
            {
                Console.WriteLine("Ошибка: название не может быть пустым.");
                return;
            }

            var task = new TaskItem
            {
                Id = _nextId++,
                Title = title,
                IsCompleted = false,
                CreatedAt = DateTime.Now
            };

            _tasks.Add(task);
            Console.WriteLine($"Задача #{task.Id} добавлена!");
        }

        static void ShowTasks()
        {
            if (!_tasks.Any())
            {
                Console.WriteLine("Список задач пуст.");
                return;
            }

            Console.WriteLine("\nВаши задачи:");

            // 5. LINQ: фильтрация и сортировка (сначала невыполненные)
            var sorted = _tasks.OrderBy(t => t.IsCompleted).ThenBy(t => t.CreatedAt);

            foreach (var task in sorted)
            {
                Console.WriteLine(task);
            }

            // 6. LINQ: Подсчет статистики
            int completedCount = _tasks.Count(t => t.IsCompleted);
            Console.WriteLine($"\nИтого: {_tasks.Count} задач. Выполнено: {completedCount}.");
        }

        static void CompleteTask()
        {
            Console.Write("Введите ID задачи для отметки: ");
            if (!int.TryParse(Console.ReadLine(), out int id))
            {
                Console.WriteLine("Ошибка: введите число.");
                return;
            }

            // 7. LINQ: поиск элемента (FirstOrDefault)
            var task = _tasks.FirstOrDefault(t => t.Id == id);
            if (task == null)
            {
                Console.WriteLine("Задача не найдена.");
                return;
            }

            task.IsCompleted = true;
            Console.WriteLine($"Задача \"{task.Title}\" выполнена!");
        }

        static void DeleteTask()
        {
            Console.Write("Введите ID задачи для удаления: ");
            if (!int.TryParse(Console.ReadLine(), out int id))
            {
                Console.WriteLine("Ошибка: введите число.");
                return;
            }

            // 8. LINQ: поиск и удаление
            var task = _tasks.FirstOrDefault(t => t.Id == id);
            if (task == null)
            {
                Console.WriteLine("Задача не найдена.");
                return;
            }

            _tasks.Remove(task);
            Console.WriteLine($"Задача \"{task.Title}\" удалена.");
        }

        // 9. Работа с JSON (сериализация) и файловой системой
        static async Task SaveTasks()
        {
            try
            {
                string json = JsonSerializer.Serialize(_tasks, new JsonSerializerOptions { WriteIndented = true });
                await File.WriteAllTextAsync("tasks.json", json);
                Console.WriteLine("Данные сохранены в tasks.json");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Ошибка сохранения: {ex.Message}");
            }
        }

        // 10. Асинхронная загрузка (имитация)
        static async Task LoadTasksAsync()
        {
            try
            {
                if (File.Exists("tasks.json"))
                {
                    string json = await File.ReadAllTextAsync("tasks.json");
                    var loaded = JsonSerializer.Deserialize<List<TaskItem>>(json);
                    if (loaded != null && loaded.Any())
                    {
                        _tasks = loaded;
                        _nextId = _tasks.Max(t => t.Id) + 1;
                        Console.WriteLine($"Загружено {_tasks.Count} задач.");
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Ошибка загрузки: {ex.Message}");
            }
            await Task.CompletedTask; // Чтобы компилятор не ругался
        }
    }
}

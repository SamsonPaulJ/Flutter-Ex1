void main() {
  runApp(const CalorieApp());
}

class CalorieApp extends StatelessWidget {
  const CalorieApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Calorie Tracker',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.green),
      ),
      home: const CalorieHomePage(),
    );
  }
}

class CalorieHomePage extends StatefulWidget {
  const CalorieHomePage({super.key});

  @override
  State<CalorieHomePage> createState() => _CalorieHomePageState();
}

class _CalorieHomePageState extends State<CalorieHomePage> {
  final TextEditingController _foodController = TextEditingController();
  final TextEditingController _calorieController = TextEditingController();

  final List<Map<String, dynamic>> _foodList = [];
  int totalCalories = 0;

  void _addFood() {
    final food = _foodController.text.trim();
    final caloriesText = _calorieController.text.trim();

    if (food.isEmpty || caloriesText.isEmpty) {
      _showSnack('Please enter food name and calories');
      return;
    }

    final calories = int.tryParse(caloriesText);
    if (calories == null || calories < 0) {
      _showSnack('Enter valid calorie number');
      return;
    }

    setState(() {
      _foodList.add({'food': food, 'calories': calories});
      totalCalories += calories;
    });

    _foodController.clear();
    _calorieController.clear();
  }

  void _showSnack(String message) {
    ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text(message)));
  }

  void _deleteFood(int index) {
    setState(() {
      totalCalories -= _foodList[index]['calories'] as int;
      _foodList.removeAt(index);
    });
  }

  void _showSummary() {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => SummaryPage(
          totalCalories: totalCalories,
          totalItems: _foodList.length,
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Calorie Tracker"),
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
      ),
      body: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Column(
          children: [
            TextField(
              controller: _foodController,
              decoration: const InputDecoration(
                labelText: 'Food Item',
                border: OutlineInputBorder(),
              ),
            ),
            const SizedBox(height: 10),
            TextField(
              controller: _calorieController,
              keyboardType: TextInputType.number,
              decoration: const InputDecoration(
                labelText: 'Calories',
                border: OutlineInputBorder(),
              ),
            ),
            const SizedBox(height: 10),
            ElevatedButton(
              onPressed: _addFood,
              child: const Text('Add Item'),
            ),
            const SizedBox(height: 15),
            Expanded(
              child: _foodList.isEmpty
                  ? const Center(child: Text('No food items added yet.'))
                  : ListView.builder(
                      itemCount: _foodList.length,
                      itemBuilder: (context, index) {
                        final item = _foodList[index];
                        return Card(
                          child: ListTile(
                            title: Text('${item['food']}'),
                            subtitle: Text('Calories: ${item['calories']}'),
                            trailing: IconButton(
                              icon: const Icon(Icons.delete, color: Colors.red),
                              onPressed: () => _deleteFood(index),
                            ),
                          ),
                        );
                      },
                    ),
            ),
            const SizedBox(height: 10),
            Text(
              'Total Calories: $totalCalories',
              style: const TextStyle(
                  fontSize: 18, fontWeight: FontWeight.bold, color: Colors.green),
            ),
            const SizedBox(height: 10),
            ElevatedButton(
              onPressed: _showSummary,
              style: ElevatedButton.styleFrom(backgroundColor: Colors.green),
              child: const Text('View Summary'),
            ),
          ],
        ),
      ),
    );
  }
}

class SummaryPage extends StatelessWidget {
  final int totalCalories;
  final int totalItems;

  const SummaryPage({
    super.key,
    required this.totalCalories,
    required this.totalItems,
  });

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Summary")),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text(
              "Today's Summary",
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 20),
            Text(
              "Total Food Items: $totalItems",
              style: const TextStyle(fontSize: 20),
            ),
            const SizedBox(height: 10),
            Text(
              "Total Calories: $totalCalories kcal",
              style: const TextStyle(
                  fontSize: 22, fontWeight: FontWeight.bold, color: Colors.green),
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                Navigator.pop(context);
              },
              child: const Text("Back to Tracker"),
            ),
          ],
        ),
      ),
    );
  }
}

# ✈️ FlyWise: Flight Route & Fare Comparator

[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IuXd4k6Y)

> **A smart flight route planner that compares earliest-arrival and cheapest itineraries across multiple cabin classes.**

A Python CLI application that implements a Google Flights-style route comparison engine. Uses Dijkstra's shortest-path algorithm and efficient data structures to find optimal flight itineraries while enforcing real-world constraints like minimum layover times.

---

## 🚀 Quick Start

```bash
# Run a comparison query
python src/flight_planner.py compare data/flights_global.txt ICN SFO 08:00
```

**Output:**
```
Comparison for ICN → SFO (earliest departure 08:00, layover ≥ 60 min)

Mode                      Cabin      Dep    Arr    Duration   Stops  Total Price
--------------------------------------------------------------------------------------
Earliest arrival          economy    08:30  20:15  11h45m     1      717
Cheapest (Economy)        economy    09:00  22:25  13h25m     1      655
Cheapest (Business)       business   15:00  23:30  8h30m      0      1600
Cheapest (First)          first      09:00  22:25  13h25m     1      2620
```

---

## 📋 Features

### ✅ Multiple Search Strategies
- **Earliest-arrival**: Finds the route that arrives soonest
- **Cheapest Economy/Business/First**: Finds the lowest-cost route for each cabin class

### ✅ Smart Routing
- Implements Dijkstra's shortest-path algorithm with time constraints
- Enforces 60-minute minimum layovers between connections
- Automatically finds multi-stop itineraries when direct flights aren't optimal

### ✅ Flexible Input
- Supports both **TXT** (space-separated) and **CSV** file formats
- Auto-detects format based on file extension
- Handles comments (`#`) and blank lines gracefully

### ✅ Robust Error Handling
- Validates airport codes and time formats
- Clear error messages for invalid inputs
- Handles missing routes gracefully

---

## 📦 Installation

### Prerequisites
- Python 3.11 or higher
- No external dependencies (uses only Python standard library)

### Setup
```bash
# Clone the repository
cd data-structures-fall-2025-project-3-notyouradhee

# Ready to run! No installation needed.
```

---

## 🎯 Usage

### Basic Syntax
```bash
python src/flight_planner.py compare FLIGHT_FILE ORIGIN DEST DEPARTURE_TIME
```

### Parameters
- `FLIGHT_FILE` - Path to flight schedule file (.txt or .csv)
- `ORIGIN` - Three-letter origin airport code (e.g., ICN, LHR, JFK)
- `DEST` - Three-letter destination airport code
- `DEPARTURE_TIME` - Earliest departure time in HH:MM format (24-hour)

---

## 💡 Examples

### Example 1: International Route
```bash
python src/flight_planner.py compare data/flights_global.txt ICN LAX 08:00
```
Finds routes from Seoul (ICN) to Los Angeles (LAX) departing at or after 8:00 AM.

### Example 2: Using CSV Format
```bash
python src/flight_planner.py compare data/flights_global.csv LHR DXB 08:00
```
Finds routes from London (LHR) to Dubai (DXB) using CSV input file.

### Example 3: Transcontinental Route
```bash
python src/flight_planner.py compare data/flights_global.txt LHR JFK 06:00
```
Compares transatlantic flight options.

### Example 4: No Route Available
```bash
python src/flight_planner.py compare data/flights_global.txt SFO ICN 08:00
```
Shows "N/A" for all modes when no valid route exists.

---

## 🏗️ Implementation Details

### Data Structures

#### Graph Representation
- **Nodes**: Airport codes (strings)
- **Edges**: Flight objects with departure/arrival times and prices
- **Structure**: Adjacency list using `Dict[str, List[Flight]]`

```python
graph["ICN"] = [Flight(...), Flight(...), ...]  # All flights departing from ICN
```

#### Hash Tables (Dictionaries)
The implementation uses 4 key dictionaries:

1. **`flights_from: Dict[str, List[Flight]]`** - Adjacency list for graph
2. **`best_arrival: Dict[str, int]`** - Earliest arrival time for each airport (earliest-arrival search)
3. **`best_cost: Dict[tuple, int]`** - Minimum cost to reach each (airport, time) state (cheapest search)
4. **`previous: Dict[str, Flight]`** - Path reconstruction for backtracking

### Algorithms

#### Earliest-Arrival Search
- **Algorithm**: Modified Dijkstra's shortest path
- **Cost Metric**: Arrival time at destination
- **Time Complexity**: O(E log V) where E=flights, V=airports
- **Space Complexity**: O(V)

#### Cheapest-Itinerary Search
- **Algorithm**: Modified Dijkstra's shortest path
- **Cost Metric**: Total price in specified cabin class
- **Time Complexity**: O(E log V)
- **Space Complexity**: O(E)
- **Key Feature**: Enforces time/layover constraints while optimizing for price

### Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| Build Graph | O(E) | O(E + V) | Single pass through flights |
| Earliest-Arrival | O(E log V) | O(V) | Dijkstra with priority queue |
| Cheapest-Route | O(E log V) | O(E) | Dijkstra with state tracking |

Where: E = number of flights, V = number of airports

---

## 🧪 Testing

### Run All Tests
```bash
# Set Python path
$env:PYTHONPATH="src"  # Windows PowerShell
# OR
export PYTHONPATH="src"  # Linux/Mac

# Run all tests
pytest tests/ -v
```

### Test Results
✅ **All 26 tests passing (100%)**

- ✅ 13 time & parsing tests
- ✅ 10 graph & search tests  
- ✅ 3 itinerary & output tests

### Test Categories
- **Time parsing and validation**
- **Flight file parsing** (TXT and CSV)
- **Graph construction**
- **Earliest-arrival routing**
- **Cheapest-itinerary routing with layover constraints**
- **Output formatting**
- **Edge case handling**

---

## 📁 Project Structure

```
├── src/
│   └── flight_planner.py       # Main implementation (all functionality)
├── tests/
│   ├── test_time_and_parsing.py
│   ├── test_graph_and_search.py
│   └── test_itinerary_and_output.py
├── data/
│   ├── flights_global.txt      # Sample flight data (TXT format, 982 flights)
│   └── flights_global.csv      # Sample flight data (CSV format)
├── README.md                   # This file
└── PROJECT_SPEC.md             # Full project specification
```

---

## 📝 Input File Formats

### Plain Text Format (.txt)
```
# Comments start with #
ORIGIN DEST FLIGHT_NUMBER DEPART ARRIVE ECONOMY BUSINESS FIRST
ICN    NRT  FW101         08:30  11:00  350     800      1400
NRT    SFO  FW202         12:30  20:30  500     1100     2000
```

### CSV Format (.csv)
```csv
origin,dest,flight_number,depart,arrive,economy,business,first
ICN,NRT,FW101,08:30,11:00,350,800,1400
NRT,SFO,FW202,12:30,20:30,500,1100,2000
```

**Field Specifications:**
- **Times**: HH:MM in 24-hour format (converted to minutes since midnight internally)
- **Prices**: Integer values for each cabin class
- **Airports**: Three-letter codes (IATA-style)

---

## 🛡️ Edge Cases Handled

✅ Unknown airport codes - Clear error message  
✅ Invalid time format - Validation with specific error  
✅ Malformed flight data - Line number reported  
✅ Empty input files - Detected and reported  
✅ No valid itinerary - Shows "N/A" in output  
✅ No flights after requested time - Handled gracefully  
✅ Insufficient layover time - Connections automatically filtered  
✅ Arrival before departure - Rejected during parsing  
✅ Blank lines and comments - Ignored  
✅ Both TXT and CSV formats - Auto-detected  

---

## 🎓 Key Design Decisions

1. **Dijkstra's Algorithm**: Optimal for single-source shortest path with non-negative weights
2. **State Representation**: Track (airport, time) tuples to handle time-dependent routing
3. **Frozen Dataclasses**: Immutable Flight objects prevent accidental modification
4. **Dual Format Support**: Demonstrates real-world adaptability
5. **Comprehensive Validation**: User-friendly errors with context

---

## ⚡ Performance

Typical performance on `flights_global.txt` (982 flights, ~100 airports):

- **File Loading**: < 50ms
- **Graph Building**: < 10ms
- **Single Search**: < 150ms
- **Full Compare Query**: < 500ms (4 searches + formatting)

---

## 📚 Documentation

- **[PROJECT_SPEC.md](PROJECT_SPEC.md)** - Complete project specification with all requirements
- **[src/flight_planner.py](src/flight_planner.py)** - Fully documented source code with docstrings
- **[tests/](tests/)** - Comprehensive test suite

---

## 👨‍💻 Author

Implementation by notyouradhee for Data Structures Fall 2025 - Project 3

---

## 📄 License

Academic project for Ulsan College - Data Structures course

---

*Last Updated: December 13, 2025*  
*Status: ✅ Complete - All tests passing (26/26)*

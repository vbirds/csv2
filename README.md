<p align="center">
  <img height="75" src="img/logo.png" alt="csv2"/>
</p>

`csv2` is a fast, single-threaded CSV parser written in `C++17`.

## Highlights
* Single-threaded - No additional worker threads.
* Fast - Use of `std::string_view` to minimize allocations/copies.
* Lazy evaluated - Tokenization is not performed until the row is read using `read_row()`.
* Single header file - Just include `<csv2/reader.hpp>`
* MIT License.

```cpp
#include <csv2/reader.hpp>
using namespace csv2;

int main() {

  // Configure CSV reader
  Reader csv {
    option::Delimiter{','},
    option::SkipInitialSpace{true}
    // ...
  };

  // Open CSV file
  if (csv.open("foo.csv")) {
    
    // Access header
    auto header = csv.header();

    // Iterate over all data rows
    Row next;
    while (csv.read_row(next)) {
      // Do something with row

      // for (size_t i = 0; i < header.size(); i++)
      //   std::cout << header[i] << ": " << next[i] << "\n";
    }
  }
}
```

## Options

| Property | Data Type | Description |
|--------------------|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `option::Delimiter` | `char` | specifies the character which should separate fields (aka columns). Default = `','` |
| `option::QuoteCharacter` | `char` | specifies the character to use as the quoting character. Default = `'"'` |
| `option::SkipInitialSpace` | `bool` | specifies how to interpret whitespace immediately following the delimiter; if `false`, it means that whitespace immediately after a delimiter should be treated as part of the following field. Default = `false` |
| `option::IgnoreColumns` | `std::vector<std::string>` | specifies the list of columns to ignore. Default = ```{}``` - no column ignored |
| `option::ColumnNames` | `std::vector<std::string>` | specifies the list of column names. This is useful when the first row of the CSV isn't a header Default = ```{}``` |
| `option::SkipEmptyRows` | `bool` | specifies how empty rows should be interpreted. If this is set to true, empty rows are skipped. Default = ```false``` |

## Performance Benchmark

Compile `benchmark/main.cpp` using:

```bash
cd benchmark
g++ -I../include -O3 -std=c++17 -o main main.cpp
./main <csv_file>
```

### Measurement

This benchmarks measures two execution times:

* **Measurement 1 (M1)**: Load input CSV file and build the list of lines (`std::vector<std::string>`).
* **Measurement 2 (M2)**: Iterate over all the lines, tokenize each line and construct a `std::vector<std::vector<std::string_view>>` rows list.

### Hardware 

```
MacBook Pro (15-inch, 2019)
Processor: 2.4 GHz 8-Core Intel Core i9
Memory: 32 GB 2400 MHz DDR4
Operating System: macOS Catalina version 10.15.3
```

### Results (as of 19 APR 2020)

| Dataset | File Size | Rows | Cols | M1 | M2 | Total Time |
|:---     |       ---:|  ---:|  ---:|  ---:|  ---:|  ---:|
| [Denver Crime Data](https://www.kaggle.com/paultimothymooney/denver-crime-data) | 111 MB | 479,100 | 19 | 0.166s | 0.768s | 0.934s |
| [AirBnb Paris Listings](https://www.kaggle.com/juliatb/airbnb-paris) | 196 MB | 141,730 | 96 | 0.236s | 0.512s | 0.749s |
| [2015 Flight Delays and Cancellations](https://www.kaggle.com/usdot/flight-delays) | 574 MB | 5,819,079 | 31 | 1.071s | 9.316s | 10.387s |
| [StackLite: Stack Overflow questions](https://www.kaggle.com/stackoverflow/stacklite) | 870 MB | 17,203,824 | 7 | 2.459s | 14.532s | 16.991s |
| [Used Cars Dataset](https://www.kaggle.com/austinreese/craigslist-carstrucks-data) | 1.4 GB | 539,768 | 25 | 1.597s | 1.999s | 3.596s |
| [Title-Based Semantic Subject Indexing](https://www.kaggle.com/hsrobo/titlebased-semantic-subject-indexing) | 3.7 GB | 12,834,026 | 4 | 4.869s | 10.133s | 15.002s |
| [Bitcoin tweets - 16M tweets](https://www.kaggle.com/alaix14/bitcoin-tweets-20160101-to-20190329) | 4 GB | 47,478,748 | 9 | 7.431s | 10.456s | 17.887s |
| [DDoS Balanced Dataset](https://www.kaggle.com/devendra416/ddos-datasets) | 6.3 GB | 12,794,627 | 85 | 7.938s | 42.951s | 50.890s |
| [Seattle Checkouts by Title](https://www.kaggle.com/city-of-seattle/seattle-checkouts-by-title) | 7.1 GB | 34,892,623 | 11 | 11.118s | 48.818s | 59.937s |
| [SHA-1 password hash dump](https://www.kaggle.com/urvishramaiya/have-i-been-pwnd) | 11 GB | 2,62,974,240 | 2 | 32.370s | 107.985s | 140.356s |
| [DOHUI NOH scaled_data](https://www.kaggle.com/seaa0612/scaled-data) | 16 GB | 504,779 | 3213 | 21.121s | 59.328s | 80.450s |

## Compiling Tests

```bash
mkdir build && cd build
cmake -DCSV2_TEST=ON ..
make
cd test
./test
```

## Contributing
Contributions are welcome, have a look at the [CONTRIBUTING.md](CONTRIBUTING.md) document for more information.

## License
The project is available under the [MIT](https://opensource.org/licenses/MIT) license.

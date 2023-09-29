```c++
#include <iostream>

int main()
{
    int x = 13;

    std::cout << "x = " << x << std::endl;
    return 0;
}
```

```python
#!/usr/bin/python3
"""
@brief  SideFuzzer
@author {\_Sidewinder_/}
@brief  Black box fuzzer
"""

import sys
from PyQt5.QtWidgets import QApplication
from gui.window import Window
from fuzzer import Fuzzer

if __name__ == "__main__":
    app = QApplication(sys.argv)

    fuzzer = Fuzzer()
    window = Window()

    window.start_fuzzing.connect(fuzzer.on_start_fuzzing)
    window.generate_random_strings.connect(fuzzer.on_generate_random_string)

    fuzzer.update_progress_bar.connect(window.on_update_progress_bar)
    fuzzer.fuzzing_output_ready.connect(window.on_fuzzing_output_ready)
    fuzzer.payload_ready.connect(window.on_payload_ready)

    app.exec_()

```
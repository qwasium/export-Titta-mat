# export-Titta-mat

**NOTICE**: THIS REPO IS NO LONGER MAINTAINED.

In February 2024, Titta has officially implemented a tsv export function.
Please see [dcnieho/Titta](https://github.com/dcnieho/Titta.git) for details.

---

Tested on:

- MATLAB R2023a Update 5
- GNU Octave 6.4.0
- Ubuntu 22.04

---

## Description

This is a code for exporting output structure of [dcnieho/Titta](https://github.com/dcnieho/Titta.git) to delimited files.

This code will create a table(MATLAB) or struct(GNU Octave) where each field only contains a single row of data with the same length for easy data export.

I encourage the user to dig into the code and use inheritance/polyphormism to add custom actions to the output.

I know this code is very much incomplete, so please feel free to open a pull request.

## Quick Start

```matlab
% load titta output (shown as data.mat here)
tittaMat = load('data.mat');

% convert to table
data = titta2delim(tittaMat);
data.main();

% write to csv
writetable(data.sessionInfo, 'sesionInfo.csv');
writetable(data.timeSeries, 'timeSeries.csv');
writetable(data.messages, 'messages.csv');
writetable(data.TobiiLog, 'TobiiLog.csv');
writetable(data.notifications, 'notifications.csv');
```

Check out [/readmeDemo](./readDemo) for more details.

- MATLAB: readmeDemo/matlabDemo.mlx
- GNU Octave: readmeDemo/octaveDemo.ipynb

## setting column names

The column names are set in the following priority. Every column name is evaluated and if it does not meet the criteria, it will fall back to the next priority level until a valid column name is found.

1. If a string or character is provided as a parameter to the method, it will be evaluated first. This is a one-time change only affecting the current method call.
2. The key map(`dictionary` or `containers.Map`) provided through `addKeyMap()` will be evaluated next. This is a permanent change affecting all method calls as long as the key map keeps existing as a class attribute. If the key map does not include a key for the current column, it will fall back to the default column name.
3. If no valid column name is provided, the default column name will be used. This is a hard-coded value in the source code.

## execution order

The code is executed in the following order:

- **`titta2delim()`**: constructor, adds key map if provided

  - `addKeyMap()`: If a `dictionary` or `containers.Map` is provided, it will be added as a class attribute `keyMap`

- **`main()`**: main function; calls the following functions in order

  - `createSessionInfo()`
    - functions to add each row of `sessionInfo`
    - `userDefinedSessionInfo()`: empty; modify this function for custom actions to `sessionInfo`
  - `createTimeSeries()`
    - functions to add each row of `timeSeries`
    - `userDefinedTimeSeries()`: empty; modify this function for custom actions to `timeSeries`
  - `createLog()`
    - functions to create `TobiiLog`, `notifications`, and `messages`
    - `userDefinedLog()`: empty; modify this function for custom actions to `TobiiLog`
  - `createCalibration()`: empty; not implemented
  - `userDefinedMain()`: modify this function for custom actions to `main()`

## NOTE

- This code is meant to be modified using inheritance/polyphormism. The user should modify the functions in `userDefinedSessionInfo()`, `userDefinedTimeSeries()`, `userDefinedLog()`, and `userDefinedMain()` to add custom actions to the output. See [/readmeDemo](./readDemo) for examples.

- Most of the methods are designed to be called independently by the user, mainly for custom or one-time operations for adding a column. Documentation for each method can be found in the docstrings of each method in the source code.

- Some methods are not implemented yet. They are described in `createSessionInfo()`, `createTimeSeries()`, and `createLog()`.

- I have not been able to read the entire source code of Titta, and lack deep understanding of how the Tobii SDK C output corresponds to Titta output. Please feel free to open a pull request if you find anything that should be fixed.

# [imgui-js](https://github.com/flyover/imgui-js)
JavaScript bindings for [Dear ImGui](https://github.com/ocornut/imgui) using [Emscripten](https://github.com/kripken/emscripten) and [TypeScript](https://github.com/Microsoft/TypeScript)

Removed dependencies such as "system". Removed example. it's broken idgaf.
Compiles to es module instead of umd. 

## Notes
All functions in the C++ ImGui namespace are exported at the top level of the module.
```typescript
import * as ImGui from "imgui-js";
```
Individual exports can be imported as well.
```typescript
import { ImVec2 } from "imgui-js";
```

In general, functions that take an address of a variable in C++ have been changed to take an access function in JavaScript.  Calling the access function with no arguments returns the variable, calling with a value sets the variable.

```typescript
type ImAccess<T> = (value?: T) => T;

let show: boolean = true;

const _show: ImAccess<boolean> = (_: boolean = show): boolean => show = _;

// get the value of show
console.log(_show()); // true

// set the value of show to false (also returns the updated value)
console.log(_show(false)); // false
```

In the following example, the address of `show` in the C++ code has been replaced with an inline arrow access function.

```c++
#include "imgui.h"
bool show = true;
void draw() {
    if (ImGui::Button("Toggle")) { show = !show; }
    if (show) {
        ImGui::Begin("My Window", &show, ImGuiWindowFlags_AlwaysAutoResize));
        ImGui::Text("Hello, World!");
        ImGui::End();
    }
}
```

```typescript
import * as ImGui from "imgui-js";
let show: boolean = true;
function draw(): void {
    if (ImGui.Button("Toggle")) { show = !show; }
    if (show) {
        ImGui.Begin("My Window", (_ = show) => show = _, ImGui.WindowFlags.AlwaysAutoResize));
        ImGui.Text("Hello, World!");
        ImGui.End();
    }
}
```

Enumerations that begin with ImGui* are also exported with ImGui removed.  So the following examples are equivalent.
```typescript
import * as ImGui from "imgui-js";
const flags: ImGui.WindowFlags = ImGui.WindowFlags.AlwaysAutoResize;
```
```typescript
import { ImGuiWindowFlags } from "imgui-js";
const flags: ImGuiWindowFlags = ImGuiWindowFlags.AlwaysAutoResize;
```

In order to minimize size of the output, the C++ library has been compiled with `IMGUI_DISABLE_OBSOLETE_FUNCTIONS` and `IMGUI_DISABLE_DEMO_WINDOWS`.

## Building
* `git clone git@github.com:flyover/imgui-js.git`
* `cd imgui-js`
* `git submodule update --init --recursive`
* download and install [Emscripten](http://kripken.github.io/emscripten-site/docs/getting_started/downloads.html)
* `npm install`
* `make`
* `make start-example-html`

## TODO
* file I/O, imgui.ini, loading external fonts
* implement ImGuiTextFilter (add support for JavaScript RegExp's)
* implement ImGuiTextBuffer (simplify to array of strings)
* fill in remainder of any missing API
* automate the Emscripten install and environment setup in npm install

## License
imgui-js is licensed under the MIT License, see [LICENSE](LICENSE.txt) for more information.

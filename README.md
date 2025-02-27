
# MAT dash

  

A simple C++ 17 library to manage low-level hooks in x86/32 bit Windows aplications.
MAT-dash by itself is not a hooking library as it depends on other libraries to create hooks.

This library offers its users a convenient way to hook functions without the need to create additional function pointers to call the original function which was hooked, thus reducing boilerplate code that it is needed to create a project utilizing low-level hooks.

  
This project is very basic, as its only temporary while [Geode](https://github.com/geode-sdk/) is in the works

  

## Usage

  

```cpp
#include <matdash.hpp>

// defines add_hook to use minhook
#include <matdash/minhook.hpp>

// lets you use mod_main
#include <matdash/boilerplate.hpp>

#include <gd.h>

using namespace cocos2d;

class MenuLayerMod : public gd::MenuLayer {
public:
    // here the name cant be `init` as that'd make it a virtual
    // which doesnt work with the current code
    bool init_() {
        if (!matdash::orig<&MenuLayerMod::init_>(this)) return false;

        auto label = CCLabelBMFont::create("Hello world!", "bigFont.fnt");
        label->setPosition(ccp(200, 200));
        addChild(label);

        return true;
    }
};

void MenuLayer_onNewgrounds(gd::MenuLayer* self, CCObject* sender) {
    std::cout << "cool!" << std::endl;
    matdash::orig<&MenuLayer_onNewgrounds>(self, sender);
}

bool GJDropDownLayer_init(gd::GJDropDownLayer* self, const char* title, float height) {
    return matdash::orig<&GJDropDownLayer_init>(self, "my own title", height * 0.5f);
}

matdash::cc::thiscall<void> PlayLayer_update(gd::PlayLayer* self, float dt) {
    matdash::orig<&PlayLayer_update>(self, dt * 0.5f);
    return {};
}

void PlayLayer_update_(gd::PlayLayer* self, float dt) {
    // another way of specifying the calling convention
    matdash::orig<&PlayLayer_update_, matdash::Thiscall>(self, dt * 0.5f);
}

void mod_main(HMODULE) {
    matdash::add_hook<&MenuLayerMod::init_>(gd::base + 0x1907b0);
    matdash::add_hook<&MenuLayer_onNewgrounds>(gd::base + 0x191e90);
    matdash::add_hook<&GJDropDownLayer_init>(gd::base + 0x113530);
    matdash::add_hook<&PlayLayer_update>(gd::base + 0x2029c0);

    // another way of specifying the calling convention
    matdash::add_hook<&PlayLayer_update_, matdash::Thiscall>(gd::base + 0x2029c0);
}
```

  

# Setup

Add the include folder to the include directories

# Credit
Thanks to [pie](https://github.com/poweredbypie) for some help on the templates and thanks to [AndreNIH](https://github.com/AndreNIH) for helping out on the calling convention encoding mechanism

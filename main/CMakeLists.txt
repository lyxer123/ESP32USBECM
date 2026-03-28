idf_component_register(SRC_DIRS "."
                    INCLUDE_DIRS ".")

# GCC 14.2 (xtensa-esp-elf) can ICE in ira while compiling esp_lcd rgb_panel on Windows.
idf_component_get_property(esp_lcd_lib esp_lcd COMPONENT_LIB)
target_compile_options(${esp_lcd_lib} PRIVATE $<$<COMPILE_LANGUAGE:C>:-O0>)

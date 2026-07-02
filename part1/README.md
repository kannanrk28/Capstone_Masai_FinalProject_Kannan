Data Type Validation Summary
=============================

Numeric columns: Inches, cpu_speed, Weight_kg, resolution_width, resolution_height, Ram, Price, hdd, ssd, flashstorage, hybrid are correctly typed.
Categorical columns: Company, TypeName, cpu_brand, cpu_name, gpu_brand, gpu_name, Memory, OpSys are correctly stored as object.
Binary columns: touchscreen, ipspanel, and retinadisplay are correctly stored as integers (0/1), though converting them to bool improves readability.

---
title: "OOP CLI Tool in Python: Beyond GUIs and Games"
date: 2023-07-24T14:00:26-04:00
draft: false
---


When learning about Object-Oriented Programming, especially in Python, you're likely to encounter examples centered around GUIs (Graphical User Interfaces) or game development. While these can be fun, it can take a while it was difficult for me to figure out how to apply this to my work where I'm mostly writing small tools or automation in Python.

Inspired by an example in "Python Object-Oriented Programming - Fourth Edition," I set out to build a Python tool that would be applicable in a more routine, work-related context, and yet effectively showcase the principles of OOP and specifically the abtract base class. The result was a functional tool for operating on ZIP files. This tool allows users to search and replace text within files inside a ZIP archive or to rename files within the ZIP based on a user-specified string.

## Leveraging Abstract Base Classes in Practical Tools

Abstract Base Classes serve as blueprints for other classes, allowing us to define methods that any child classes must implement. Using ABCs in this ZIP processing tool helped organize the code better and also made it easier to extend the functionality.

I created an `ZipProcessor` class that encapsulated shared behaviors, such as making a backup of the ZIP file and iterating through its contents. I left the definition of the file processing method to its child classes. 

## Implementing Unique Functionality Through Inheritance

Building on the Abstract Base Class, I created two child classes, `TextReplaceZipProcessor` and `FilenameReplaceZipProcessor`, each inheriting from `ZipProcessor`. These classes implemented the shared method `process_item` from the ABC and provided their unique file processing methods. This highlighted the ability to create specialized behaviors through inheritance while maintaining a clean structure and consisten interface.

## Tying it all Together and Conclusion

I also wanted to practice creating a complete tool to showcase on my GitHub. I included a command-line interface and unit tests using the `pytest` framework.

This venture into building a practical tool using object-oriented programming in Python allowed me to break out of the conventional GUI and game-oriented examples. It provided me an opportunity to delve into the more granular details of Python's OOP capabilities and allowed me to create a tool that had real-world applications. The [source code is available on my GitHub](https://github.com/lazarofraga/zipfnr), and I encourage you to explore it, tweak it, and learn more about how easy it is to use OOP concepts in Python.
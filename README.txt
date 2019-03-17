Feel free to contribute if you want! I'm always open to suggestions, so pull requests are welcome.

This web app is targeted toward students at UNC Charlotte enrolled in Computer Architecture. The labs for this course require students manually convert values from input tables into source code that the simulation software can understand. The VHDL Code Generator provides two very easy ways to quickly convert the input values from the tables given in the lab instructions into the source code needed to simulate the circuits.

The easiest way to convert the input values is to copy and paste directly from the lab instructions, and make sure it is the format as seen in the example below. If this fails for whatever reason, the fallback is to input the values into an Excel sheet, save as a CSV (comma delimited) and upload it.

To test this out, visit the site: https://vhdl-code-generator.firebaseapp.com/
Example: Copy and paste the following into the text area, and click convert text:

A
0 0 0 0 1 1 1 1
B
0 0 1 1 0 0 1 1
CIN
0 1 0 1 0 1 0 1

The user can also specify a prefix for the port names as well as specify the desired delay. Many times, we are asked to rename our ports in our code, for example, from A to SourceA. This makes things much easier.

Thanks for looking, and I hope this helps!

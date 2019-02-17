*APS Advanced Stats Delivery*
# Sequence data formats and assessing sequence quality - day 1 afternoon
#### Nicola Nadeau, Alison Wright, Victor Soria-Carrasco

In this practical you will get to grips with handelling sequence data, assessing its quality and trimming reads.

## 1. Fastq format, viewing and searching
We will start by having a look at the sequence data files you downloaded this morning. Navigate to your fastdata directory where they are located. The files are compressed (ending in ```.gz```) so you can't view them directly with ```less```. You could extract them with ```gunzip``` but then they would take up much more space and this isn't necessary for the programs we will be using, because all of these can work on compressed data. Instead we can use ```zcat```, which can unzip files "on the fly" and writes the output to  the standard output. By default this is the terminal window. Try using ```zcat``` to view one of the sequence files. You will see that the whole thing gets written to the terminal window. You probably want it to stop!!! 
### Ctrl c will stop it.

```
sharc.sheffield.ac.uk
```

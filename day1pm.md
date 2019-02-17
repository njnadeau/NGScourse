*APS Advanced Stats Delivery*
# Sequence data formats and assessing sequence quality - day 1 afternoon
#### Nicola Nadeau, Alison Wright, Victor Soria-Carrasco

In this practical you will get to grips with handelling sequence data, assessing its quality and trimming reads.

## 1. Fastq format, viewing and searching
We will start by having a look at the sequence data files you downloaded this morning. Navigate to your fastdata directory where they are located. The files are compressed (ending in ```.gz```) so you can't view them directly with ```less```. You could extract them with ```gunzip``` but then they would take up much more space and this isn't necessary for the programs we will be using, because all of these can work on compressed data. Instead we can use ```zcat```, which can unzip files "on the fly" and writes the output to  the standard output (```stdout```). By default this is the terminal window, so if we used just ```zcat``` the whole thing gets written to the terminal window. If this happens by mistake don't panic! ```Ctrl c``` will stop it, although it might take a little time for your command to take effect, the node will already have read in a huge amount of data before your command is even registered! 

Instead we can use ```|``` (called a pipe) to send the output to another program. In our case we can send it to ```less``` so we can control how we view the output
```
zcat 151019_D00248_0127_AC7HJ7ANXX_7_TP-D7-006_TP-D5-008_1.sanfastq.gz | less
```


## Create a TextPrinter class
```csharp
using System.Drawing.Printing;

namespace WinBrag
{
    public class TextPrinter
    {
        // https://learn.microsoft.com/en-us/dotnet/api/system.drawing.printing.printdocument.print?view=dotnet-plat-ext-8.0

        private Font printFont;
        private StreamReader streamToPrint;
        static string filePath;

        //public PrintingExample()
        //{
        //    Printing();
        //}

        // The PrintPage event is raised for each page to be printed.
        private void pd_PrintPage(object sender, PrintPageEventArgs ev)
        {
            float linesPerPage = 0;
            float yPos = 0;
            int count = 0;
            float leftMargin = ev.MarginBounds.Left;
            float topMargin = ev.MarginBounds.Top;
            String line = null;

            // Calculate the number of lines per page.
            linesPerPage = ev.MarginBounds.Height /
               printFont.GetHeight(ev.Graphics);

            // Iterate over the file, printing each line.
            while (count < linesPerPage &&
               ((line = streamToPrint.ReadLine()) != null))
            {
                yPos = topMargin + (count * printFont.GetHeight(ev.Graphics));
                ev.Graphics.DrawString(line, printFont, Brushes.Black,
                   leftMargin, yPos, new StringFormat());
                count++;
            }

            // If more lines exist, print another page.
            if (line != null)
                ev.HasMorePages = true;
            else
                ev.HasMorePages = false;
        }

        public static void PrintFile(string filename)
        {
            TextPrinter tp = new();
            tp.InternalPrintFile(filename);
        }

        public static void PrintText(string contents)
        {
            TextPrinter tp = new();
            tp.InternalPrintText(contents);
        }

        // Print the file.
        public void InternalPrintFile(string filename)
        {
            try
            {
                streamToPrint = new StreamReader(filename);
                try
                {
                    printFont = new Font("Arial", 10);
                    PrintDocument pd = new PrintDocument();
                    pd.PrintPage += new PrintPageEventHandler(pd_PrintPage);
                    // Print the document.
                    pd.Print();
                }
                finally
                {
                    streamToPrint.Close();
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void InternalPrintText(string contents)
        {
            //PrintPreviewDialog ppd = new();
            //ppd.ShowDialog();

            //return;

            PrintDialog pdlg = new PrintDialog();
            pdlg.AllowSelection = false;
            if (pdlg.ShowDialog() == DialogResult.OK)
            {
                try
                {
                    // https://stackoverflow.com/questions/8047064/convert-string-to-system-io-stream
                    // System.IO.MemoryStream mStream = new System.IO.MemoryStream(System.Text.Encoding.UTF8.GetBytes( contents));
                    //streamToPrint = new StreamReader(filename);
                    streamToPrint = new StreamReader(new System.IO.MemoryStream(System.Text.Encoding.UTF8.GetBytes(contents)));
                    try
                    {
                        printFont = new Font("Arial", 10);
                        PrintDocument pd = new PrintDocument();
                        pd.PrinterSettings = pdlg.PrinterSettings;
                        pd.PrintPage += new PrintPageEventHandler(pd_PrintPage);
                        // Print the document.
                        pd.Print();
                    }
                    finally
                    {
                        streamToPrint.Close();
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show(ex.Message);
                }
            }
        }


        /*
        Use the PrinterSettings.PrinterName property to specify which printer should print the document.

        The Print method prints the document without using a print dialog. Use a PrintDialog when you 
        want to offer the user the ability to choose print settings. 
        */
    }
}
```

## Send in-memory text to printer
```csharp
            TextPrinter.PrintText(textBox1.Text);
```

## Send a text file to printer
```csharp
            TextPrinter.PrintFile(filename);
```

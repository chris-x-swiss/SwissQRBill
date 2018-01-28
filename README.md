# Swiss QR Bill

Open-source Java library to generate Swiss QR bills.

For demonstration pruposes, there is code for an Angular UI and a web service in addition to the Java library.
[Create your first QR bill](https://www.codecrete.net/qrbill).

## Introduction

The Swiss QR bill is the new QR code based payment format that will replace the current payment slip.
The new payment slip with the QR code is either directly printed at the bottom of an invoice
or added to the invoice on a separate sheet. The payer scans the QR code with his/her mobile banking app
to initiate the payment. No other data needs to be entered. The payment just needs to be confirmed.

The invoicing party can easily synchronize the received payment with the accounts-receivable accounting as
they payment comes with a full set of data including the reference number used on the invoice. So the Swiss QR bill
is convenient for the payer and payee.

## Getting started

The QR bill generator is available at Maven Central. To use it, just add it to your Maven or Gradle project.

If you are using *Maven*, add the below dependency to your `pom.xml`:

    <dependency>
        <groupId>net.codecrete.qrbill</groupId>
        <artifactId>qrbill-generator</artifactId>
        <version>0.9.1</version>
    </dependency>

If you are using *Gradle*, add the below dependency to your *build.gradle* file:

    compile group: 'net.codecrete.qrbill', name: 'qrbill-generator', version: '0.9.1'

To generate a QR bill, you first fill in the `Bill` data structure and then call `QRBill.generate`:

    package net.codecrete.qrbill.examples;

    import net.codecrete.qrbill.generator.Address;
    import net.codecrete.qrbill.generator.Bill;
    import net.codecrete.qrbill.generator.QRBill;

    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Path;
    import java.nio.file.Paths;
    import java.time.LocalDate;

    public class QRBillExample {

        public static void main(String[] args) {

            // Setup bill
            Bill bill = new Bill();
            bill.setLanguage(Bill.Language.fr);
            bill.setAccount("CH4431999123000889012");
            bill.setAmount(199.95);
            bill.setCurrency("CHF");

            // Set creditor
            Address creditor = new Address();
            creditor.setName("Robert Schneider AG");
            creditor.setStreet("Rue du Lac");
            creditor.setHouseNo("1268/2/22");
            creditor.setPostalCode("2501");
            creditor.setTown("Biel");
            creditor.setCountryCode("CH");
            bill.setCreditor(creditor);

            // Set final creditor
            Address finalCreditor = new Address();
            finalCreditor.setName("Robert Schneider Services Switzerland AG");
            finalCreditor.setStreet("Rue du Lac");
            finalCreditor.setHouseNo("1268/3/1");
            finalCreditor.setPostalCode("2501");
            finalCreditor.setTown("Biel");
            finalCreditor.setCountryCode("CH");
            bill.setFinalCreditor(finalCreditor);

            // more bill data
            bill.setDueDate(LocalDate.of(2019, 10, 31));
            bill.setReferenceNo("RF18539007547034");
            bill.setAdditionalInfo(null);

            // Set debtor
            Address debtor = new Address();
            debtor.setName("Pia-Maria Rutschmann-Schnyder");
            debtor.setStreet("Grosse Marktgasse");
            debtor.setHouseNo("28");
            debtor.setPostalCode("9400");
            debtor.setTown("Rorschach");
            debtor.setCountryCode("CH");
            bill.setDebtor(debtor);

            byte[] svg = QRBill.generate(bill, QRBill.BillFormat.A6LandscapeSheet, QRBill.GraphicsFormat.SVG);

            Path path = Paths.get("qrbill.svg");
            try {
                Files.write(path, svg);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
            System.out.println("QR bill saved at " + path.toAbsolutePath());
        }
    }

## API Documention

See Javadoc [API Documentation](https://www.codecrete.net/qrbill-javadoc/).

## QR Code

For the generation of the QR code itself, [Nayuki's QR code generator](https://github.com/nayuki/QR-Code-generator) is used.
As it is not available on Maven Central, it is included in this library (with a modified package name to avoid conflicts).
The library has also been released under the MIT license.

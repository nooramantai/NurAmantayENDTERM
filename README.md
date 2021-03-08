# NurAmantayENDTERM
There is my endterm project about chat bot with database
#1

package com.company;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class Main {

    public static void main(String[] args) {
//        Connection connection = null;
//        Statement statement = null;


//        try {
//            Class.forName("org.postgresql.Driver");
//            connection = DriverManager.getConnection("jdbc:postgresql://localhost:5432/endterm","postgres", "030101501636");
//            System.out.println(" Database was successful opened");
//
//            statement = connection.createStatement();
//            String sql = "CREATE TABLE card_info " + "(ID serial," + " full_name TEXT NOT NULL, " + " card_numbers INT NOT NULL, " + " cvv INT NOT NULL)";
//            statement.executeUpdate(sql);
//            statement.close();
//            connection.close();
//        }  catch( Exception e ) {
//            System.err.println( e.getClass().getName()+": "+ e.getMessage() );
//            System.exit(0);
//        }


    }}
#2
package com.company;
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import javax.swing.text.*;
import javax.swing.text.*;
public class ChatBot extends JFrame implements ActionListener {
    final String TITLE_OF_PROGRAM = "Delivery of food";
    //sizes and decorations that you can see below
    final int START_LOCATION = 30;
    final int WINDOW_WIDTH = 400;
    final int WINDOW_HEIGHT = 520;
    final String CHB_AI = "AI";
    final String BTN_ENTER = "Enter";

    JTextPane dialogue; // area for dialog
    JCheckBox ai;       // enable/disable AI
    JTextField message; // field for entering messages
    Bot bot;     // chat-bot class (in bot package)
    SimpleAttributeSet botStyle; // style bot text

    public static void main(String[] args) {
        new ChatBot();
    }

    ChatBot() {
        //methods of JFRAME
        setTitle(TITLE_OF_PROGRAM);//open title of program
        setDefaultCloseOperation(EXIT_ON_CLOSE);//determine the end of code, if you tab the x on the above right site
        setBounds(START_LOCATION, START_LOCATION, WINDOW_WIDTH, WINDOW_HEIGHT);//coordinates and sizes of chat that we set above
        // area for dialog
        dialogue = new JTextPane();
        dialogue.setEditable(false);
        dialogue.setContentType("text/html");
        JScrollPane scrollBar = new JScrollPane(dialogue);
        // style for bot messages
        botStyle = new SimpleAttributeSet();
        StyleConstants.setItalic(botStyle, true);
        StyleConstants.setForeground(botStyle, Color.blue);
        //StyleConstants.setAlignment(botStyle, StyleConstants.ALIGN_RIGHT);
        // panel for checkbox, message field and button
        JPanel bp = new JPanel();
        bp.setLayout(new BoxLayout(bp, BoxLayout.X_AXIS));
        ai = new JCheckBox(CHB_AI);
        ai.doClick();//on the left below site of the chat
        message = new JTextField();
        message.addActionListener(this);//place to write message
        JButton enter = new JButton(BTN_ENTER);//
        enter.addActionListener(this);

        // adding all elements to the window
        bp.add(ai);
        bp.add(message);
        bp.add(enter);
        add(BorderLayout.CENTER, scrollBar);
        add(BorderLayout.SOUTH, bp);
        setVisible(true);//add graph window
        bot = new Bot(); // creating bot object
    }


    @Override
    public void actionPerformed(ActionEvent event) {
        if (message.getText().trim().length() > 0) {// if we do not write anything “enter” would not work
            try {
                StyledDocument doc = dialogue.getStyledDocument();
                doc.insertString(doc.getLength(), message.getText() + "\n",
                        new SimpleAttributeSet());//that text go to the panel
                doc.insertString(doc.getLength(),
                        TITLE_OF_PROGRAM.substring(0, 9) +
                                bot.sayInReturn(message.getText(), ai.isSelected()) + "\n",
                        botStyle);
            } catch(Exception e) { }
        }
        message.setText("");
        message.requestFocusInWindow();
    }
}
#3
package com.company;
import java.sql.*;
import java.util.*;
import java.util.Date;
import java.util.regex.*;
import java.util.Scanner;
public class Bot {
    Connection connection = null;
    Statement statement = null;
    Scanner sc = new Scanner(System.in);
    final String[] COMMON_PHRASES = {//common phrases that will use if there is unknown command
            "Sorry, we don't have such a dish on our menu",
            "Sorry, I do not understand you.",
    };
    final String[] ELUSIVE_ANSWERS = {//some more set of answers for case if the message finish with ?
            "Ok, can you repeat what do you want to order now?"
    };

    final Map<String, String> PATTERNS_FOR_ANALYSIS = new HashMap<String, String>() {{//that massive for answering to questions

        put("hi", "hello");//first word that writes human, if that word would be in the long message of human it would work
        put("good morning", "hello");//second one is the key that is for next massive below
        put("good afternoon", "hello");
        put("good evening", "hello");
        put("hello", "hello");
        put("hola", "hello");
        put("how are you", "hay");
        // order
        put("order", "order");
        put("menu", "menu");
        // breakfast
        put("what\\s.*breakfast", "breakfast");
        //lunch
        put("what\\s.*lunch", "lunch");
        //dinner
        put("what\\s.*dinner", "dinner");
        //desert
        put("have\\s.*десерт", "desert");
        // howareyou

        put("pizza", "fast food");


        put("хочу все", "fast food1");


        put("хиросима", "fast food2");


        //breakfast
        put("english", "english breakfast");

        put("bavarian", "bavarian breakfast");

        put("country", "country breakfast");

        //dinner
        put("fettuccine", "fettuccine dinner");

        put("chicken", "chicken dinner");

        put("soup", "lentil soup dinner");

        //desert
        put("tiramisu", "tiramisu desert");

        //type of payment
        put("by card", "payment");
        put("cash", "payment1");

        // bye
        put("bye", "bye");
        put("goodbye", "bye");

        put("/write card info", "add info");
        put("/write contact info", "add info");
        put("what\\s.*time", "whattime");
    }};
    final Map<String, String> ANSWERS_BY_PATTERNS = new HashMap<String, String>() {{
        //that is second massive
        put("hello", "Hi, nice to meet you.");//first one is a key, that is from first massive
        put("order", "Yes, I'm listening to you. What do you want?.");//and second is an answer of bot
        put("menu", "there are some meals for lunch, breakfast, dinner and desert");
        put("breakfast", "For breakfast, we have a Bavarian breakfast, an English breakfast and a very popular country breakfast :)");
        put("lunch", "For lunch, we have set all kinds of pizza for one price");
        put("dinner", "For dinner, we have lentil soup, chicken in sweet and sour sauce,fettuccine with mushrooms :)");
        put("desert", "I recommend you try our tiramisu, it is delicious.");
        //lunch
        put("fast food", "Oooh good choice, it will be done. From you 1,750 tenge. Will you pay in cash or by card?");
        put("fast food1", "Oo you are planning a party. From you 5675tg. Will you pay in cash or by card?");
        put("fast food2", "You are probably our regular customer because you know that this is the best choice for us." +
                " From you 8800tg. Will you pay in cash or by card?");

        put("english breakfast", "Oh, good. From you 990tg. Will you pay in cash or by card?");
        put("bavarian breakfast", "Alright. From you 1050tg. Will you pay in cash or by card?");
        put("country breakfast", "Clever decision. From you 1200tg. Will you pay in cash or by card? ");

        put("fettuccine dinner", "Ok. From you 1890tg. Will you pay in cash or by card?");

        put("chicken dinner", "Nice. From you 1400 tg. Will you pay in cash or by card? ");

        put("lentil soup dinner", "Fine. From you 890tg. Will you pay in cash or by card? ");
        put("tiramisu desert", "Delicate tiramisu. From you 1900tg. Will you pay in cash or by card? ");

        put("hay", "I am great, what do you want to order?");


        put("payment", "Please, write command '/write card info' fill information about your card");
        put("payment1", "OK. write command '/write contact info' for case if we need to contact with you. Within 35 - 40 minutes the deliveryman will call you, wait.");

        put("bye","Goodbye.");
    }};
    Pattern pattern; // for regexp
    Random random; // for random answers
    Date date; // for date and time

    public Bot() {
        random = new Random();
        date = new Date();
    }

    public String sayInReturn(String msg, boolean ai) throws Exception {//boolean ai для того чтобы вопрос с ответом были правильно

        String say = (msg.trim().endsWith("?"))?//method trim for deleting spaces, endwith check if the message with ?
                ELUSIVE_ANSWERS[random.nextInt(ELUSIVE_ANSWERS.length)]://one of the elusive answers randomly
                COMMON_PHRASES[random.nextInt(COMMON_PHRASES.length)];
        if (ai) {//if ai turned on
            String message =
                    String.join(" ", msg.toLowerCase().split("[ {,|.}?]+"));//delete all  "[ {,|.}?]+"
            for (Map.Entry<String, String> o : PATTERNS_FOR_ANALYSIS.entrySet()) {//entrySet give chance to be
                pattern = Pattern.compile(o.getKey());//for compilation
                if (pattern.matcher(message).find())//to check if in the code keys as put("...","key")
                    if (o.getValue().equals("add info")){//if key equal to "add info" then it will use DBMS
                        while (true){//typical code for DBMS as in assignment 4
                            System.out.println("Choose option");
                            System.out.println("1.Contact Info");
                            System.out.println("2.Card Info");
                            System.out.println("0.Exit");
                            int choice=sc.nextInt();
                            while (choice==1 &&  choice == 2 &&choice==0){
                                System.out.println("Please choose only from possible options 0-2");
                                choice=sc.nextInt();
                            }
                            switch (choice){
                                case 1->ContactInfo.ContactInfoes();
                                case 2->CardInfo.CardInfoes();
                                case 0->{
                                    System.exit(0);
                                }
                                default -> throw new IllegalStateException("Unexpected value: " + choice);
                            }
                        }
                    }
                    else if (o.getValue().equals("whattime")) return date.toString();//that key for checking time, one more interesting thing
                    else return ANSWERS_BY_PATTERNS.get(o.getValue());//get answer by key
            }
        }
        return say;
    }
}

#4
package com.company;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.Statement;
import java.util.Scanner;

public class CardInfo {
    private static final Connection connection = null;

    public static void CardInfoes() throws Exception {
        Connection connection = null;
        Statement statement = null;
        Scanner sc = new Scanner(System.in);
        try {
            String url = "jdbc:postgresql://localhost:5432/endterm";
            String username = "postgres";
            String password = "030101501636";
            Class.forName("org.postgresql.Driver");
            connection = DriverManager.getConnection("jdbc:postgresql://localhost:5432/endterm","postgres","030101501636");
            connection.setAutoCommit(false);
            statement = connection.createStatement();



            System.out.println("Your Full name, please:");
            String name = sc.next();

            System.out.println("Please write numbers of your card:");
            int c_number = sc.nextInt();

            System.out.println("Please write cvv of your card:");
            int cvv = sc.nextInt();

            connection = DriverManager.getConnection(url, username, password);
            String sql = "INSERT INTO card_info ( full_name,card_numbers, cvv) "
                    + "VALUES (?,?,?);";
            PreparedStatement preparedStatement = connection.prepareStatement(sql);

            preparedStatement.setString(1, name);
            preparedStatement.setInt(2, c_number);
            preparedStatement.setInt(3, cvv);
            preparedStatement.execute();

        }
        catch (Exception ex) {
            System.out.println("Connection failed...");
            System.out.println(ex);
        }


    }

}
#5
package com.company;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.Statement;
import java.sql.*;
import java.util.Scanner;

public class ContactInfo {
    private static final Connection connection = null;

    public static void ContactInfoes() throws Exception {
    Connection connection = null;
    Statement statement = null;
    Scanner sc = new Scanner(System.in);
    try {
        String url = "jdbc:postgresql://localhost:5432/endterm";
        String username = "postgres";
        String password = "030101501636";
        Class.forName("org.postgresql.Driver");
        connection = DriverManager.getConnection("jdbc:postgresql://localhost:5432/endterm","postgres","030101501636");
        connection.setAutoCommit(false);
        statement = connection.createStatement();



        System.out.println("Your Name, please:");
        String name = sc.next();

        System.out.println("Your phone number, please:");
        int ph_number = sc.nextInt();

        connection = DriverManager.getConnection(url, username, password);
        String sql = "INSERT INTO contact_info ( name,phone_number) "
                + "VALUES (?,?);";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);

        preparedStatement.setString(1, name);
        preparedStatement.setInt(2, ph_number);
        preparedStatement.execute();

    }catch (Exception ex) {
        System.out.println("Connection failed...");
        System.out.println(ex);

    }
}}


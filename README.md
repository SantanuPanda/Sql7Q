import java.io.*;
import java.net.*;

class Client2 {

    public static void main(String args[]) throws Exception {

        // Create client socket
        Socket s = new Socket("localhost", 8888);

        // to send data to the server
        DataOutputStream dos =
                new DataOutputStream(s.getOutputStream());

        // to read data coming from the server
        BufferedReader br =
                new BufferedReader(
                        new InputStreamReader(
                                s.getInputStream()));

        // to read data from the keyboard
        BufferedReader kb =
                new BufferedReader(
                        new InputStreamReader(System.in));

        String str, str1;

        // repeat as long as exit is not typed at client
        while (!(str = kb.readLine()).equals("exit")) {

            // send to the server
            dos.writeBytes(str + "\n");

            // receive from the server
            str1 = br.readLine();

            System.out.println("Server says : " + str1);
        }

        // close connection
        dos.close();
        br.close();
        kb.close();
        s.close();
    }
}












import java.io.*;
import java.net.*;

class Server2 {

    public static void main(String args[]) throws Exception {

        // Create server Socket
        ServerSocket ss = new ServerSocket(8888);

        // connect it to client socket
        Socket s = ss.accept();
        System.out.println("Connection established");

        // to send data to the client
        PrintStream ps =
                new PrintStream(s.getOutputStream());

        // to read data coming from the client
        BufferedReader br =
                new BufferedReader(
                        new InputStreamReader(
                                s.getInputStream()));

        // to read data from the keyboard
        BufferedReader kb =
                new BufferedReader(
                        new InputStreamReader(System.in));

        String str, str1;

        // server executes continuously
        while ((str = br.readLine()) != null) {

            System.out.println("Client says: " + str);

            // read reply from server keyboard
            str1 = kb.readLine();

            // send to client
            ps.println(str1);
        }

        // close connection
        ps.close();
        br.close();
        kb.close();
        s.close();
        ss.close();
    }
}
import java.io.*;
import java.net.*;

public class EchoClient {

    private final static String HOSTNAME = "localhost";
    private final static int PORT = 45000;

    public static void main(String args[]) {

        String sockin;

        try {
            // Create socket connection
            Socket csock = new Socket(InetAddress.getLocalHost(), PORT);

            // Input from keyboard
            BufferedReader br = new BufferedReader(
                    new InputStreamReader(System.in));

            // Input from server
            BufferedReader br_sock = new BufferedReader(
                    new InputStreamReader(csock.getInputStream()));

            // Output to server
            PrintStream ps = new PrintStream(csock.getOutputStream());

            System.out.println("Connected to " + HOSTNAME + " on port " + PORT);
            System.out.println("Start echoing... type 'end' to terminate");

            while ((sockin = br.readLine()) != null) {

                System.out.println("Sending to server: " + sockin);
                ps.println(sockin);

                if (sockin.equals("end"))
                    break;
                else
                    System.out.println("Echoed from server: " + br_sock.readLine());
            }

            // Close resources
            br.close();
            br_sock.close();
            ps.close();
            csock.close();

        } catch (UnknownHostException e) {
            System.out.println(e.toString());
        } catch (IOException ioe) {
            System.out.println(ioe);
        }
    }
}













import java.io.*;
import java.net.*;

public class EchoServer {

    private final static int PORT = 45000;

    public static void main(String args[]) {

        String echoin;

        ServerSocket svrsoc = null;
        Socket soc = null;
        BufferedReader br = null;
        PrintStream ps = null;

        System.out.println("Listening on port " + PORT);

        try {
            // Create Server Socket
            svrsoc = new ServerSocket(PORT);

            // Wait for client connection
            soc = svrsoc.accept();

            // Input from client
            br = new BufferedReader(
                    new InputStreamReader(soc.getInputStream()));

            // Output to client
            ps = new PrintStream(soc.getOutputStream());

            System.out.println("Connection accepted");
            System.out.println("Connected for echo:");

            while ((echoin = br.readLine()) != null) {

                System.out.println("Server received: " + echoin +
                        " | Sending back to client");

                ps.println(echoin);

                if (echoin.equals("end")) {
                    System.out.println("Client disconnected");
                    break;
                }
            }

            // Close resources
            br.close();
            ps.close();
            soc.close();
            svrsoc.close();

        } catch (IOException ioe) {
            System.out.println(ioe);
        }
    }
}

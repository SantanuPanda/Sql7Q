import java.io.*;
import java.net.*;

public class Client {

    private Socket socket = null;
    private BufferedReader input = null;
    private DataOutputStream out = null;

    // Constructor with IP address and port
    public Client(String address, int port) {

        try {
            // Establish connection
            socket = new Socket(address, port);
            System.out.println("Connected to Server");

            // Input from terminal
            input = new BufferedReader(new InputStreamReader(System.in));

            // Output to server
            out = new DataOutputStream(socket.getOutputStream());

            String message = "";

            // Read until "Over" is entered
            while (!message.equalsIgnoreCase("Over")) {
                message = input.readLine();
                out.writeUTF(message);
                out.flush();
            }

            // Close resources
            input.close();
            out.close();
            socket.close();

            System.out.println("Connection Closed");

        } catch (UnknownHostException e) {
            System.out.println("Host not found: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("I/O Error: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        new Client("127.0.0.1", 5000);
    }
}











// SERVER SIDE PROGRAM
// Demonstrating Server-side Programming

import java.net.*;
import java.io.*;

public class Server {

    private ServerSocket serverSocket = null;
    private Socket socket = null;
    private DataInputStream in = null;

    // Constructor with port
    public Server(int port) {

        try {
            // Start server
            serverSocket = new ServerSocket(port);
            System.out.println("Server started");
            System.out.println("Waiting for a client...");

            // Accept client connection
            socket = serverSocket.accept();
            System.out.println("Client connected");

            // Input stream from client
            in = new DataInputStream(
                    new BufferedInputStream(socket.getInputStream()));

            String message = "";

            // Read until "Over"
            while (true) {
                message = in.readUTF();
                System.out.println("Client: " + message);

                if (message.equalsIgnoreCase("Over")) {
                    break;
                }
            }

            System.out.println("Closing connection...");

            // Close resources
            in.close();
            socket.close();
            serverSocket.close();

        } catch (IOException e) {
            System.out.println("Server Error: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        new Server(5000);
    }
}
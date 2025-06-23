package server;

import java.io.*;
import java.net.*;

public class ClientHandler extends Thread {
    private Socket socket;
    private PrintWriter out;
    private BufferedReader in;

    public ClientHandler(Socket socket) {
        this.socket = socket;
    }

    public void run() {
        try {
            in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            out = new PrintWriter(socket.getOutputStream(), true);

            String name = in.readLine();
            System.out.println(name + " joined the chat.");
            ChatServer.broadcast(name + " has joined.", this);

            String msg;
            while ((msg = in.readLine()) != null) {
                ChatServer.broadcast(name + ": " + msg, this);
            }
        } catch (IOException e) {
            System.out.println("Connection lost.");
        } finally {
            ChatServer.removeClient(this);
            try {
                socket.close();
            } catch (IOException e) { }
        }
    }

    void sendMessage(String msg) {
        out.println(msg);
    }
}

#include <iostream>
#include <string>
#include <thread>
#include <netinet/in.h>
#include <unistd.h>
using namespace std;

void handleClient(int clientSocket) {
    char buffer[1024] = {0};
    read(clientSocket, buffer, 1024);
    string response = 
        "HTTP/1.1 200 OK\nContent-Type: text/html\n\n"
        "<html><body><h1>Hello from C++ Server!</h1></body></html>";
    write(clientSocket, response.c_str(), response.size());
    close(clientSocket);
}

int main() {
    int server_fd = socket(AF_INET, SOCK_STREAM, 0);
    sockaddr_in address; int opt = 1; int addrlen = sizeof(address);
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(8080);

    bind(server_fd, (struct sockaddr*)&address, sizeof(address));
    listen(server_fd, 3);
    cout << "Server running on port 8080...\n";

    while (true) {
        int clientSocket = accept(server_fd, (struct sockaddr*)&address, (socklen_t*)&addrlen);
        thread(handleClient, clientSocket).detach();
    }
}

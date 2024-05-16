import random

class Node:
    def __init__(self, id, energy):
        self.id = id
        self.energy = energy
        self.messages_received = []

    def receive_message(self, message):
        if self.check_energy() and self.validate_message(message):
            self.messages_received.append(message)
            print(f"Message {message['id']} received by Node {self.id}")
            self.energy -= 1  # Enerji tüketimi simülasyonu
            return True
        return False

    def check_energy(self):
        return self.energy > 0

    def validate_message(self, message):
        # Güvenlik kontrolü (basit simülasyon)
        return 'content' in message

class Network:
    def __init__(self):
        self.nodes = [Node(i, random.randint(5, 10)) for i in range(5)]
        self.edges = {(i, j): random.random() for i in range(5) for j in range(5) if i != j}

    def send_message(self, message, start_node):
        current_node = self.nodes[start_node]
        for step in range(5):  # Maksimum 5 adımda iletilecek
            next_node = self.select_next_hop(current_node)
            if next_node and next_node.receive_message(message):
                print(f"Message {message['id']} successfully delivered to Node {next_node.id}")
                return
            print(f"Message {message['id']} failed to deliver from Node {current_node.id}")
        print("Failed to deliver the message after 5 steps.")

    def select_next_hop(self, current_node):
        neighbors = [node for node in self.nodes if (current_node.id, node.id) in self.edges and node.check_energy()]
        if not neighbors:
            return None
        # Enerjiye göre filtreleme ve rastgele seçim
        return max(neighbors, key=lambda x: x.energy * self.edges[(current_node.id, x.id)])

# Ağın oluşturulması ve mesaj iletimi
network = Network()
message = {'id': 1, 'content': 'Hello from Node 0'}
network.send_message(message, 0)

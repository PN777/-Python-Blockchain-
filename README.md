# -Python-Blockchain-
import hashlib
import time

class Block:
    def __init__(self, index, previous_hash, transactions, timestamp=None):
        self.index = index
        self.previous_hash = previous_hash
        self.timestamp = timestamp or time.time()
        self.transactions = transactions
        self.hash = self.calculate_hash()

    def calculate_hash(self):
        block_string = f"{self.index}{self.previous_hash}{self.timestamp}{self.transactions}".encode()
        return hashlib.sha256(block_string).hexdigest()


class Blockchain:
    def __init__(self):
        self.chain = [self.create_genesis_block()]

    def create_genesis_block(self):
        return Block(0, "0", "Genesis Block", time.time())

    def get_latest_block(self):
        return self.chain[-1]

    def add_block(self, new_block):
        new_block.previous_hash = self.get_latest_block().hash
        new_block.hash = new_block.calculate_hash()
        self.chain.append(new_block)

    def is_chain_valid(self):
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i-1]

            if current_block.hash != current_block.calculate_hash():
                return False
            if current_block.previous_hash != previous_block.hash:
                return False
        return True


class Transaction:
    def __init__(self, sender, recipient, amount):
        self.sender = sender
        self.recipient = recipient
        self.amount = amount

    def __repr__(self):
        return f"{self.sender} -> {self.recipient}: {self.amount}"


# Example usage
if __name__ == "__main__":
    my_blockchain = Blockchain()

    # Add some transactions
    transaction1 = Transaction("Alice", "Bob", 50)
    transaction2 = Transaction("Bob", "Charlie", 25)

    # Add the first block
    block1 = Block(1, my_blockchain.get_latest_block().hash, [transaction1])
    my_blockchain.add_block(block1)

    # Add the second block
    block2 = Block(2, my_blockchain.get_latest_block().hash, [transaction2])
    my_blockchain.add_block(block2)

    # Output the blockchain
    for block in my_blockchain.chain:
        print(f"Block {block.index}:")
        print(f"Previous Hash: {block.previous_hash}")
        print(f"Transactions: {block.transactions}")
        print(f"Hash: {block.hash}")
        print("-" * 20)

    # Check if the blockchain is valid
    print("Blockchain valid?", my_blockchain.is_chain_valid())

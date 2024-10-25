# memory_puzzle_codealpha
memory puzzle game using python

import tkinter as tk
import random
import time

class puzzleGame:
    def __init__(self, master):
        self.master = master
        self.master.title("Memory Puzzle Game")
        
        self.cards = []
        self.f_card = None
        self.s_card = None
        self.lb= False
        self.matched_pairs = 0
        self.time_left = 40
        
        self.card_images = ['🍎', '🍌', '🍇', '🍉', '🍊', '🍓', '🍍', '🍈']
        self.create_widgets()
        self.start_game()

    def create_widgets(self):
        self.timer_label = tk.Label(self.master, text=f"Time left: {self.time_left}s", font=('Helvetica', 16))
        self.timer_label.pack(pady=10)

        self.grid_frame = tk.Frame(self.master)
        self.grid_frame.pack()

        self.restart_button = tk.Button(self.master, text="Restart", command=self.start_game)
        self.restart_button.pack(pady=20)

        self.cards_buttons = []
        for i in range(4):
            row = []
            for j in range(4):
                button = tk.Button(self.grid_frame, text='', width=6, height=3, command=lambda r=i, c=j: self.flip_card(r, c))
                button.grid(row=i, column=j, padx=5, pady=5)
                row.append(button)
            self.cards_buttons.append(row)

    def create_cards(self):
        self.cards = random.sample(self.card_images * 2, len(self.cards_buttons) ** 2)

    def flip_card(self, row, col):
        if self.lb:
            return
        
        button = self.cards_buttons[row][col]
        card_index = row * 4 + col
        
        if button['text'] == '' and self.f_card != button:
            button.config(text=self.cards[card_index], state='disabled')
            
            if not self.f_card:
                self.f_card = button
                return
            
            self.s_card = button
            self.lb = True
            self.master.after(500, self.check_for_match)

    def check_for_match(self):
        if self.f_card['text'] == self.s_card['text']:
            self.matched_pairs += 1
            if self.matched_pairs == len(self.card_images):
                self.timer_label.config(text="Congratulations! You've matched all pairs!")
        else:
            self.f_card.config(text='', state='normal')
            self.s_card.config(text='', state='normal')
        
        self.f_card = None
        self.s_card = None
        self.lb = False

    def update_timer(self):
        if self.time_left > 0:
            self.time_left -= 1
            self.timer_label.config(text=f"Time left: {self.time_left}s")
            self.master.after(1000, self.update_timer)
        else:
            self.timer_label.config(text="Time is up! Try again.")
            self.lb = True

    def start_game(self):
        self.create_cards()
        self.matched_pairs = 0
        self.time_left = 40
        self.lock_board = False
        self.timer_label.config(text=f"Time left: {self.time_left}s")
        
        for row in self.cards_buttons:
            for button in row:
                button.config(text='', state='normal')

        self.update_timer()

if __name__ == "__main__":
    root = tk.Tk()
    game = puzzleGame(root)
    root.mainloop()

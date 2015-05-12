---
layout: post
title: "Intro to Ruby & Web Dev: Tic-Tac-Toe Procedural Game"
tags: tealeaf
---
1. Come up with requirements/specifications - that will determine your own scope.
2. Application logic, sequence of steps.
3. Translation of those steps into code.
4. Run code to verfiy logic.=end

# Sequence of steps

1. Draw a board
2. Assign Player to "x"
3. Assign Computer to "o"
Loop until a winner or all squares are taken
4. Player 1 picks an empty square
Check for winner
5. Player 2 picks an empty square
6. Check for winner
7. If there is a winner, show winner; else "It's a tie"

{%highlight ruby%}
require 'pry'

#create a way to allow players to input choice on tictactoe board, hash
def initialize_board
  b = {}
  (1..9).each {|position| b[position] = '  '}
  b
end

#define board position where player/computer choice will go
def draw_board(b)
  system 'clear'
  puts " #{b[1]} | #{b[2]} | #{b[3]} "
  puts "--------------"
  puts " #{b[4]} | #{b[5]} | #{b[6]} "
  puts "--------------"
  puts " #{b[7]} | #{b[8]} | #{b[9]} "
end

def empty_position(b)
  b.select {|k, v| v == '  '}.keys

end

def player_picks_square(b)
  puts "Pick a square (1 - 9):"
  #make sure your gets.chomp is getting the state that your hash key is
  position = gets.chomp.to_i
  b[position] = 'x'
  #binding.pry
end

def computer_picks_square(b)
  position = empty_position(b).sample
  b[position] = 'o'
end

def check_winner(b)
winning_lines = [[1,2,3], [4,5,6], [7,8,9], [1,4,7], [2,5,8], [3,6,9], [1,5,9], [3,5,7]]
winning_lines.each do |line|
    if b[line[0]] == 'x' and b[line[1]] == 'x' and b[line[2]] == 'x'
      return 'Player'
    elsif b[line[0]] == 'o' and b[line[1]] == 'o' and b[line[2]] == 'o'
      return 'Computer'
    else
      return nil
    end
  end
end

#sets local variable b from the initialize method (inner_scope) equal to board, a variable defined in the outter scope
board = initialize_board
draw_board(board)


#loop until
begin
  player_picks_square(board)
  computer_picks_square(board)
  draw_board(board)
  winner = check_winner(board)
end until winner || empty_position(board).empty?

if winner
  puts "#{winner} won!"
else
  puts "It's a tie!"
end
{%endhighlight%}



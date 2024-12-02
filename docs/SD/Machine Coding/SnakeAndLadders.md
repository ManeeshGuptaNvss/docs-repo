
```java title="Board.java"
package SnakeAndLadder;

import java.util.HashMap;

public class Board {
    private final HashMap<Integer, Integer> snakes;
    private final HashMap<Integer, Integer> ladders;
    private final int numOfCells;
     public Board(int numOfCells){
         snakes = new HashMap<>();
         ladders = new HashMap<>();
         this.numOfCells = numOfCells;
     }

    public int getNumOfCells() {
        return numOfCells;
    }

    public void addSnake(int start, int end){
         if(start <= end) {
             System.out.println("Snake should have start position greater than end");
         }
         snakes.put(start,end);
     }
    public void addLadder(int start, int end){
        if(start >= end) {
            System.out.println("Ladder should have start position less than end");
        }
        ladders.put(start,end);
    }

    public int updatePlayerPosition(Player player, int diceVal){
         int currentPlayerPosition = player.getCurrentPos();
         int expectedPosition = currentPlayerPosition + diceVal;

         if(expectedPosition>numOfCells){
             expectedPosition = numOfCells;
         }
         else if(snakes.containsKey(expectedPosition)){
             System.out.println(player.getName() +": is bitten by a snake at "+expectedPosition);
             expectedPosition = snakes.get(expectedPosition);
         }
         else if(ladders.containsKey(expectedPosition)) {
             System.out.println(player.getName() + ": climbed a ladder at " + expectedPosition);
             expectedPosition = ladders.get(expectedPosition);
         }
         player.setCurrentPos(expectedPosition);
         System.out.println(player.getName() + ": moved to " + expectedPosition);
         return expectedPosition;
    }

}

```

```java
package SnakeAndLadder;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class Game {
    List<Player> playerList;
    Random random;
    Board board;
    public Game(int numOfCells){
        playerList = new ArrayList<>();
        random = new Random();
        board = new Board(numOfCells);
    }

    public void createPlayer(String name){
        Player player = new Player(name);
        playerList.add(player);
    }

    public void addSnakeToBoard(int startPos, int endPos){
        board.addSnake(startPos,endPos);
    }
    public void addLadderToBoard(int startPos, int endPos){
        board.addLadder(startPos,endPos);
    }

    public void startGame(){
        System.out.println("!!!!!!!!!!!!!!!!!!!GAME STARTED !!!!!!!!!!!!!");
        int currentPlayerIndex = 0;
        Player currentPlayer;
        while(true){
            currentPlayer = playerList.get(currentPlayerIndex);
            // throwing a dice
            int diceValue = random.nextInt(6)+1;
            System.out.println(currentPlayer.getName()+": thrown the dice --> "+diceValue);
            // update player position
            int updatedPlayerPos = board.updatePlayerPosition(currentPlayer,diceValue);
            // check if the player has finished the game
            if(updatedPlayerPos==board.getNumOfCells()){
                System.out.println(currentPlayer.getName()+": won the game!!!");
                break;
            }
            // get next player index
            currentPlayerIndex = (currentPlayerIndex+1)%(playerList.size());
        }
        System.out.println("!!!!!!!!!!!!!!!!!!!GAME OVER !!!!!!!!!!!!!");
    }
}

```

```java
package SnakeAndLadder;

public class Main {
    public static void main(String[] args) {
        // create a game
        Game game = new Game(100);

        // add players
        game.createPlayer("Maneesh Gupta");
        game.createPlayer("Lokesh");
        game.createPlayer("Bhargav");

        // add snakes
        game.addSnakeToBoard(21,12);
        game.addSnakeToBoard(13,11);
        game.addSnakeToBoard(1,2);

        // add ladders
        game.addLadderToBoard(2,12);
        game.addLadderToBoard(9,19);

        game.startGame();
    }
}

```

```java
package SnakeAndLadder;

public class Player {
    private String name;
    private int currentPos;

    public Player(String name) {
        this.name = name;
        currentPos = 1;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setCurrentPos(int currentPos) {
        this.currentPos = currentPos;
    }

    public String getName() {
        return name;
    }

    public int getCurrentPos() {
        return currentPos;
    }
}

```
import java.awt.Canvas;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.Graphics2D;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseListener;
import java.awt.event.MouseMotionListener;
import java.awt.event.MouseEvent;
import java.awt.image.BufferStrategy;
import java.util.Random;

import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.SwingUtilities;

import java.io.*;
import java.util.Scanner;
import static java.lang.Math.abs;


public class Overworld implements Runnable{
   
   final int gridSize = 50;
   int mapSizex = 0;
   int mapSizey = 0;
   
   //int player[] = {0, 0};
   int movestart[] = {0, 0};
   int X = 0;
   int Y = 0;
   int x = 0;
   int y = 0;
   boolean esc = false;
   String action = "";
   String turn = "player";
   Random rand = new Random();
   //boolean selected = false;
   //boolean confirm = false;
   boolean click = false;
   boolean execute = false;
   //boolean target = false;
   int timer = 0;
   
   JFrame frame;
   Canvas canvas;
   BufferStrategy bufferStrategy;
   
   private class Menu{
      String string;
      int i;
      Menu next;
      Menu(String string, int i){
	     this.string = string;
	     this.i = i;
	  }
   }
   
   Menu menu = null;
   Menu combatmenu = null;
   int items;
   
	private class MoveRange {
		int x;
		int y;
		int distance;
		MoveRange next;
		MoveRange(int x, int y, int distance){
			this.x = x;
			this.y = y;
			this.distance = distance;
			next = null;
		}
	}
	
	MoveRange moverange = null;
	
	private class AiSearchRange {
		int x;
		int y;
		int distance;
		AiSearchRange next;
		AiSearchRange back;
		AiSearchRange(int x, int y, int distance, AiSearchRange back){
			this.x = x;
			this.y = y;
			this.distance = distance;
			this.back = back;
			next = null;
		}
	}
	
	AiSearchRange aisearchrange = null;
	
	private class Obstacle {
		int x;
		int y;
		Obstacle next;
		Obstacle(int x, int y){
			this.x = x;
			this.y = y;
			next = null;
		}
	}
	
	Obstacle obstacle = null;
	
	private class Unit {
		int x;
		int y;
		int maxHP;
		int HP;
		int damage;
		double accuracy;
		int movespeed;
		boolean action;
		Unit next;
		Unit(int x, int y, int HP, int damage, double accuracy, int movespeed){
			this.x = x;
			this.y = y;
			this.maxHP = HP;
			this.HP = HP;
			this.damage = damage;
			this.accuracy = accuracy;
			this.movespeed = movespeed;
			action = true;
		}
	}
	
    Unit player = null;
    Unit selected = null;
    
	private class AIUnit {
		int x;
		int y;
		int maxHP;
		int HP;
		int damage;
		double accuracy;
		int movespeed;
		boolean action;
		int aisearchrange;
		AIUnit next;
		AIUnit(int x, int y, int HP, int damage, double accuracy, int movespeed, int aisearchrange){
			this.x = x;
			this.y = y;
			this.maxHP = HP;
			this.HP = HP;
			this.damage = damage;
			this.accuracy = accuracy;
			this.movespeed = movespeed;
			this.aisearchrange = aisearchrange;
			action = true;
		}
	}
	

	AIUnit enemy = null;
	AIUnit aiselected = null;
	
   public Overworld(){
      frame = new JFrame("Basic Game");
      JPanel panel = (JPanel) frame.getContentPane();
      panel.setPreferredSize(new Dimension(1000, 1000));
      panel.setLayout(null);
      
      canvas = new Canvas();
      canvas.setBounds(0, 0, 1020, 1020);
      canvas.setIgnoreRepaint(true);
      
      panel.add(canvas);
      
      canvas.addMouseListener(new MouseControl());
      canvas.addMouseMotionListener(new MyClass());
      canvas.addKeyListener(new KeyControl());
      
      frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
      frame.pack();
      frame.setResizable(false);
      frame.setVisible(true);
      
      canvas.createBufferStrategy(2);
      bufferStrategy = canvas.getBufferStrategy();
      
      canvas.requestFocus();
   }
   
        
   private class MouseControl extends MouseAdapter{
      public void mousePressed(MouseEvent event){
    	  if(click == false && SwingUtilities.isLeftMouseButton(event)){
    		  X = event.getX();
    		  Y = event.getY();
    	      click = true;
    	  }
    	  if(SwingUtilities.isRightMouseButton(event)){
    		  esc = true;
    	  }

      }
      public void mouseReleased(MouseEvent event){
    	  click = false;
    	  execute = false;
      }
   }
   
   public class MyClass implements MouseMotionListener {

	    public void mouseMoved(MouseEvent e) {
	       x = e.getX();
	       y = e.getY();
	    }

	    public void mouseDragged(MouseEvent e) {
	       //do something
	    }
	}
   
   public class KeyControl extends JFrame implements KeyListener {
	    public void keyPressed(KeyEvent e) {
	       if (e.getKeyCode() == KeyEvent.VK_ESCAPE && esc == false) {
	          esc = true;
	       }
	    }

		@Override
		public void keyReleased(KeyEvent arg0) {

		}

		@Override
		public void keyTyped(KeyEvent arg0) {

		}
   }

   
   long desiredFPS = 60;
    long desiredDeltaLoop = (1000*1000*1000)/desiredFPS;
    
   boolean running = true;
   
   public void run(){
      
      long beginLoopTime;
      long endLoopTime;
      long currentUpdateTime = System.nanoTime();
      long lastUpdateTime;
      long deltaLoop;
      
      while(running){
         beginLoopTime = System.nanoTime();
         
         
         
         lastUpdateTime = currentUpdateTime;
         currentUpdateTime = System.nanoTime();
         update((int) ((currentUpdateTime - lastUpdateTime)/(1000*1000)));
         render();
         endLoopTime = System.nanoTime();
         deltaLoop = endLoopTime - beginLoopTime;
           
           if(deltaLoop > desiredDeltaLoop){
               //Do nothing. We are already late.
           }else{
               try{
                   Thread.sleep((desiredDeltaLoop - deltaLoop)/(1000*1000));
               }catch(InterruptedException e){
                   //Do nothing
               }
           }	
      }
   }
   
   private void render() {
      Graphics2D g = (Graphics2D) bufferStrategy.getDrawGraphics();
      g.clearRect(0, 0, mapSizex*gridSize, mapSizey*gridSize);
      render(g);
      g.dispose();
      bufferStrategy.show();
   }
   
   //TESTING
   //private double x = 0;
   
   /**
    * Rewrite this method for your game
    */
   protected void update(int deltaTime){
	  if(execute == false && click == true){
	     String[] args = {};
         Game.main(args);
         execute = true;
	  }
   }
   
   /**
    * Rewrite this method for your game
    */
   protected void render(Graphics2D g){

   }
   
   public static void main(String [] args){
      Overworld ex = new Overworld();
      new Thread(ex).start();
   }

}

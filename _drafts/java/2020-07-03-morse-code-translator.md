---
title: Morse Code Translator in Java
categories: [java]
tags: [basic-programming, code-interview]
maths: 1
toc: 1
snippet: 1
date: 2020-07-03
comment: 1
---

{% highlight java linenos %}
{% endhighlight %}

{% highlight java linenos %}
/**
 * This program is a morse code translator that will translate a given sentence in English to it's morse code
 * equivalent. Characters in the same word will be separated by a space in morse code. Spaces will be
 * separated by "|" in morse code.
 * 
 * @version 1.0
 * @date June 25 2020
 */
import java.util.NoSuchElementException;
import java.util.Scanner;
import java.util.regex.*;

public class MorseCodeTranslator {
	static Pattern p;
	static Matcher m;
	static String alphaNumericInput;
	static Scanner scanner = new Scanner(System.in);
	
	static String [] alphaNumeric = {
			"A",
			"B",
			"C",
			"D",
			"E",
			"F",
			"G",
			"H",
			"I",
			"J",
			"K",
			"L",
			"M",
			"N",
			"O",
			"P",
			"Q",
			"R",
			"S",
			"T",
			"U",
			"V",
			"W",
			"X",
			"Y",
			"Z",
			"1",
			"2",
			"3",
			"4",
			"5",
			"6",
			"7",
			"8",
			"9",
			"0"
	};
	
	static String [] morseAlphaNumeric = {
			".-", //A
			"-...", //B
			"-.-.", //C
			"-..", //D
			".", //E
			"..-.", //F
			"--.", //G
			"....", //H
			"..", //I
			".---", //J
			"-.-", //K
			".-..", //L
			"--", //M
			"-.", //N
			"---", //O
			".--.", //P
			"--.-", //Q
			".-.", //R
			"...", //S
			"-", //T
			"..-", //U
			"...-", //V
			".--", //W
			"-..-", //X
			"-.--", //Y
			"--..", //Z
			".----", // 1
			"..---", // 2
			"...--", // 3
			"....-", // 4
			".....", // 5
			"-....", // 6 
			"--...", // 7
			"---..", // 8
			"----.", // 9
			"-----" // 0
	};
	
	/**
	 * This function is a recursive binary search that will search for the index in an array where the element matches
	 * a particular test string. If the element does not exist in the array, -1 will be returned. 
	 * 
	 * <p><b>Pre-condition</b>: start index and end index are within the range of the input array size
	 * <p><b>Post-condition</b>: index of match will be return if match found, else -1 will be returned
	 * 
	 * @param input String[] array containing the elements to search over
	 * @param match String the test string to match on
	 * @param startIndex int that gives startIndex to search from
	 * @param endIndex int that gives endIndex to search to
	 * @return int giving index location, or -1 if not found
	 */
	public static int indexOf(String[] input, String match, int startIndex, int endIndex) {
		if (startIndex == endIndex) {
			if (input[startIndex].equals(match)) return startIndex;
			else return -1;
		}
		
		int midPoint = (startIndex + endIndex)/2;
		
		int leftSubtree = indexOf(input, match, startIndex, midPoint);
		int rightSubtree = indexOf(input, match, midPoint+1, endIndex);
		
		if (leftSubtree == -1 && rightSubtree == -1) return -1;
		else return Math.max(leftSubtree, rightSubtree);
	}
	
	/**
	 * Main function that drives the code
	 * 
	 * @param args command line arguments. N/A.
	 */
	public static void main(String[] args) {		
		String inputString;
		String outString;
		
		inputString = getInput();
		
		if (isAlphaNumericInput(inputString)) {
			System.out.println(convertToMorseCode(refactorAlphaNumericInput(inputString)));
		}
		else {
			while (true) {
				try {
					System.out.println(convertToAlphaNumeric(refactorMorseCodeInput(inputString)));
					break;
				} catch (IllegalArgumentException e) {
					System.out.println(" is an Invalid Morse Code Input\n");
					inputString = getInput();
				}
			}
		}
	}
	
	/**
	 * The function validateInput will validate the input to make sure we do not have more than one sentence. It works by 
	 * counting the number of punctuation characters in the string. It will throw an error if more than one punctuation character
	 * is provided as this implies more than one sentence.
	 * 
	 * <p><b>Pre-condition</b>: Input is a string without any non alphanumeric or more than one punctuation character.
	 * <p><b>Post-condition</b>: Error will be thrown if string is invalid
	 * 
	 * @param inputString String value that needs validation
	 * @throws IllegalArgumentException
	 */
	public static void validateAlphaNumericInput(String inputString) throws IllegalArgumentException {
		p = Pattern.compile("[?.!]"); // Create regex pattern to match
		m = p.matcher(inputString);
		
		int res = 0;
		while(m.find()) {
			++res;
			if (res > 1) throw new IllegalArgumentException("INVALID INPUT. Must only have one sentence");
		};
	}
	
	/**
	 * The function refactorAlphaNumericInput will refactor the alphanumeric input string to get rid of unnecessary whitespace and non 
	 * alphanumeric characters. Will also set to upper case. The refactoring is done using regex.
	 * 
	 * <p><b>Pre-condition</b>: Input is a alphanumeric string
	 * <p><b>Post-condition</b>: Will return refactored alphanumeric string
	 * 
	 * @param input String that needs refactoring
	 * @return refactored string
	 */
	public static String refactorAlphaNumericInput(String input) {
		// Set to upper case so we lower the amount of lookup values
		return input.replaceAll("\\s+", " ").replaceAll("[^\\w ]", "").toUpperCase(); 
	}
	
	/**
	 * The function refactorMorseCodeInput will refactor the morse code input string to get rid of unnecessary whitespace.
	 * 
	 * <p><b>Pre-condition</b>: Input is a morse code string
	 * <p><b>Post-condition</b>: Will return refactored morse code string
	 * 
	 * @param input String that needs refactoring
	 * @return refactored string
	 */
	public static String refactorMorseCodeInput(String input) {
		return input.replaceAll("\\s+", " ") + " "; 
	}
	
	
	/**
	 * The function isAlphaNumericInput will check if the provided input string should be classified as alphanumeric of as morse
	 * code. Any input string that has a character other than '.', '-', ' ', and '|' will be classified as alphanumeric 
	 * 
	 * <p><b>Pre-condition</b>: Input is a string
	 * <p><b>Post-condition</b>: Will return a true if input is alphanumeric
	 * 
	 * @param input String that needs refactoring
	 * @return refactored string
	 */
	public static boolean isAlphaNumericInput(String input) {
		p = Pattern.compile("[^.\\-| ]"); // Create regex pattern to match any non morse character or space
		m = p.matcher(input);
		
		if (m.find()) return true;
		return false;
	}
	
	/**
	 * The function getInput will prompt the user to enter an input string, and validate that input string. The method
	 * will continue to ask the user for the input until only one sentence is detected.
	 * 
	 * <p><b>Pre-condition</b>: None
	 * <p><b>Post-condition</b>: valid input string is returned
	 * 
	 * @return String valid input that the user has provided
	 */
	public static String getInput() {
		String inputString;
		
		while (true) {
			System.out.print("Please enter your input sentence: ");
			try {
				inputString = scanner.nextLine();
				if (isAlphaNumericInput(inputString))
					validateAlphaNumericInput(inputString);
				break;
			} catch (NoSuchElementException e) {
				System.out.println("\nERROR. Element does not exist.");
			} catch (IllegalArgumentException e) {
				System.out.println("\nINVALID. Please only one valid input sentence.\n");
			}
		}
		
		return inputString;
	}
	
	/**
	 * The function convertToMorseCode will iterate over a valid inputString and return the corresponding morse
	 * code translation. Characters in the same word will be separated by a space in morse code. Spaces will be
	 * separated by "|" in morse code. 
	 *  
	 * <p><b>Pre-condition</b>: Input is a valid string. No punctuations and no extra spaces.
	 * <p><b>Post-condition</b>: Will return a morse code translation
	 * 
	 * @param input String that needs translating
	 * @return String translated morse code string
	 */
	public static String convertToMorseCode(String inputString) {
		int selectionIndex;
		String outputString = "";
		
		for (char currChar : inputString.toCharArray()) {
			selectionIndex = indexOf(alphaNumeric, Character.toString(currChar), 0, alphaNumeric.length-1);
			if (currChar == ' ') {
				outputString += "| ";
			} else outputString += morseAlphaNumeric[selectionIndex] + " ";
		}
		
		return outputString;
	}
	
	/**
	 * The function convertToAlphaNumeric will take in a morse code string containing only the morse code characters
	 * '.', '-', '|' and ' '. It will convert this input string into the corresponding alphanumeric translation, with
	 * each character separated by a ' ', and each space separated by a '|'. If the morse code is somehow an invalid
	 * character, the function will throw an IllegalArgumentException. 
	 * 
	 * <p><b>Pre-condition</b>: Input string only contains the morse code characters '.', '-', '|', ' ' 
	 * <p><b>Post-condition</b>: Converted alphanumeric output string is returned
	 * 
	 * @param inputString String is the morse code input string to be converted
	 * @return String converted alphanumeric output string
	 * @throws IllegalArgumentException
	 */
	public static String convertToAlphaNumeric(String inputString) throws IllegalArgumentException{
		int selectionIndex;
		String outputString = "";
		String tempChar = "";
		
		for (char currChar : inputString.toCharArray()) {
			if (currChar != ' ') {
				tempChar += currChar;
				if (currChar == '|') {
					tempChar = "";
					outputString += " ";
				}
			} else {
				if (tempChar.length() != 0) {
					selectionIndex = indexOf(morseAlphaNumeric, tempChar, 0, alphaNumeric.length-1);
					if (selectionIndex == -1) {
						System.out.print("\n" + tempChar); // Print the specific character that is invalid
						throw new IllegalArgumentException("INVALID morse code");
					}
					outputString += alphaNumeric[selectionIndex];
					tempChar = "";
				}
			}
		}
		return outputString;
	}
}
{% endhighlight %}
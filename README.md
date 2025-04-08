# Laboratorio-1-EDDA
import java.util.Scanner;

public class BigVigenere {
    int[] key;
    char[][] alphabet = new char[64][64];

    // Constructor sin parámetros: solicita clave al usuario
    public BigVigenere() {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Ingrese su clave numérica:");
        String textkey = scanner.nextLine();

        key = new int[textkey.length()];
        for (int i = 0; i < textkey.length(); i++) {
            key[i] = Character.getNumericValue(textkey.charAt(i));
            if (key[i] < 0 || key[i] > 9) {
                System.out.println("Por favor ingrese una clave numérica válida.");
                System.exit(1);
            }
        }

        String textalphabet = "abcdefghijklmnñopqrstuvwxyzABCDEFGHIJKLMNÑOPQRSTUVWXYZ0123456789";

        if (textalphabet.length() != 64) {
            System.out.println("El alfabeto debe tener exactamente 64 caracteres.");
            System.exit(1);
        }

        int k = 0;
        for (int i = 0; i < 64; i++) {
            int aux = 0;
            for (int j = 0 + k; j < 64 + k; j++) {
                if (j < 64) {
                    alphabet[i][j - k] = textalphabet.charAt(j);
                } else {
                    alphabet[i][j - k] = textalphabet.charAt(aux);
                    aux++;
                }
            }
            k++;
        }
    }

    // Constructor con clave como String
    public BigVigenere(String numericKey) {
        if (numericKey == null || numericKey.isEmpty()) {
            System.out.println("Es imposible realizar esta operación.");
            System.exit(1);
        }

        key = new int[numericKey.length()];
        for (int i = 0; i < numericKey.length(); i++) {
            key[i] = Character.getNumericValue(numericKey.charAt(i));
            if (key[i] < 0 || key[i] > 9) {
                System.out.println("Por favor ingrese una clave numérica válida.");
                System.exit(1);
            }
        }

        String textalphabet = "abcdefghijklmnñopqrstuvwxyzABCDEFGHIJKLMNÑOPQRSTUVWXYZ0123456789";

        if (textalphabet.length() != 64) {
            System.out.println("El alfabeto debe tener exactamente 64 caracteres.");
            System.exit(1);
        }

        int k = 0;
        for (int i = 0; i < 64; i++) {
            int aux = 0;
            for (int j = 0 + k; j < 64 + k; j++) {
                if (j < 64) {
                    alphabet[i][j - k] = textalphabet.charAt(j);
                } else {
                    alphabet[i][j - k] = textalphabet.charAt(aux);
                    aux++;
                }
            }
            k++;
        }
    }


    public String encrypt(String message) {
        StringBuilder encryptedMessage = new StringBuilder();
        boolean found;

        for (int i = 0; i < message.length(); i++) {
            char currentChar = message.charAt(i);
            found = false;

            for (int j = 0; j < 64 && !found; j++) {
                for (int k = 0; k < 64; k++) {
                    if (alphabet[j][k] == currentChar) {
                        found = true;
                        int keyIndex = key[i % key.length];
                        int newIndex = (j * 64 + k + keyIndex) % (64 * 64);
                        encryptedMessage.append(alphabet[newIndex / 64][newIndex % 64]);
                        break;
                    }
                }
            }

            if (!found) {
                encryptedMessage.append(currentChar);
            }
        }
        return encryptedMessage.toString();
    }


    public String decrypt(String encryptedMessage) {
        StringBuilder decryptedMessageBuilder = new StringBuilder();
        boolean found;
        for (int i = 0; i < encryptedMessage.length(); i++) {
            char currentChar = encryptedMessage.charAt(i);
            found = false;

            for (int j = 0; j < 64 && !found; j++) {
                for (int k = 0; k < 64; k++) {
                    if (alphabet[j][k] == currentChar) {
                        found = true;
                        int keyIndex = key[i % key.length];

                        int newIndex = (j * 64 + k - keyIndex + (64 * 64)) % (64 * 64);
                        decryptedMessageBuilder.append(alphabet[newIndex / 64][newIndex % 64]);
                        break;
                    }
                }
            }

            if (!found) {
                decryptedMessageBuilder.append(currentChar);
            }
        }
        return decryptedMessageBuilder.toString();
    }

    public void reEncrypt() {
        Scanner scanner = new Scanner(System.in);

        System.out.println("Ingrese su mensaje encriptado: ");
        String encryptedMessage = scanner.nextLine();

        System.out.println("Ingrese su clave actual (numérica): ");
        String currentKey = scanner.nextLine();

        BigVigenere currentBigVigenere = new BigVigenere(currentKey);

        String decryptedMessage = currentBigVigenere.decrypt(encryptedMessage);
        System.out.println("Mensaje desencriptado con clave actual: " + decryptedMessage);

        System.out.println("Ingrese la nueva clave (numérica): ");
        String newKey = scanner.nextLine();

        BigVigenere newBigVigenere = new BigVigenere(newKey);

        String newEncryptedMessage = newBigVigenere.encrypt(decryptedMessage);
        System.out.println("Nuevo mensaje encriptado con la nueva clave: " + newEncryptedMessage);
    }

    public char search(int position) {
        if (position < 0 || position >= 64 * 64) {
            System.out.println("Posición fuera de rango");
        }

        int row = position / 64;
        int column = position % 64;
        return alphabet[row][column];
    }

    public char optimalSearch(int position) {
        if (position < 2048) {
            int row = position / 32;
            int column = position % 32;
            return alphabet[row][column];
        } else {
            int row = (position - 2048) / 64;
            int column = (position - 2048) % 64;

            return alphabet[row][column];
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.println("Ingrese su clave de texto inicial (numérica): ");
        String initialKey = scanner.nextLine();

        BigVigenere vigenere = new BigVigenere(initialKey);

        System.out.println("Ingrese el mensaje que desea encriptar: ");
        String message = scanner.nextLine();

        long startEncrypt = System.nanoTime();
        String encryptedMessage = vigenere.encrypt(message);
        long endEncrypt = System.nanoTime();
        System.out.println("El mensaje encriptado es: " + encryptedMessage);
        System.out.println("Tiempo de encriptado: " + (endEncrypt - startEncrypt) + " nanosegundos");

        long startDecrypt = System.nanoTime();
        String decryptedMessage = vigenere.decrypt(encryptedMessage);
        long endDecrypt = System.nanoTime();
        System.out.println("El mensaje desencriptado es: " + decryptedMessage);
        System.out.println("Tiempo de desencriptado: " + (endDecrypt - startDecrypt) + " nanosegundos");
        vigenere.reEncrypt();

        System.out.println("Desea ocupar el metodo optimalSearch?Ingrese 1 para 'Si' o 0 para 'No':");
        int option = scanner.nextInt();
        System.out.println("Ingrese la posicion del caracter que desea buscar:");
        int position = scanner.nextInt();
        if (option == 1) {
            char character = vigenere.optimalSearch(position);
            System.out.println("El caracter en esa posicion con optimalSearch es: " + character);
        }
        else if (option == 0){
            char character = vigenere.search(position);
            System.out.println("El caracter en esa posicion con search es: " + character);
        }
        else{
            System.out.println("Opcion no valida, ingrese 1 o 0");
        }
    }
    }

# BookRecommendationSystem
package mini_project;

import java.io.*;
import java.util.*;
import java.util.stream.Collectors;

class Book {
    private String id;
    private String title;
    private String author;
    private String genre;
    private String year;

    public Book(String id, String title, String author, String genre, String year) {
        this.id = id;
        this.title = title;
        this.author = author;
        this.genre = genre;
        this.year = year;
    }

    public String getId()     { return id; }
    public String getTitle()  { return title; }
    public String getAuthor() { return author; }
    public String getGenre()  { return genre; }
    public String getYear()   { return year; }

    @Override
    public String toString() {
        return id + " | " + title + " | " + author + " | " + genre + " | " + year;
    }

    public String toCSV() {
        return String.join(",", id, title, author, genre, year);
    }
}

public class Main {
    private static final String CSV_FILE = "C:\\Users\\DELL\\Documents\\java mini\\Books system.csv";
    private static List<Book> books = new ArrayList<>();

    public static void main(String[] args) {
        loadBooks();
        Scanner sc = new Scanner(System.in);
        int choice;

        do {
            System.out.println("\n=== BOOK MANAGEMENT SYSTEM ===");
            System.out.println("1. Display all books");
            System.out.println("2. Search by title");
            System.out.println("3. Search by author");
            System.out.println("4. Add new book");
            System.out.println("5. Delete a book");
            System.out.println("6. Update a book");
            System.out.println("7. Sort books by title");
            System.out.println("8. Show statistics");
            System.out.println("9. Save & Exit");
            System.out.print("Enter choice: ");
            choice = sc.nextInt();
            sc.nextLine();

            switch (choice) {
                case 1 -> displayBooks();
                case 2 -> {
                    System.out.print("Enter title keyword: ");
                    searchByTitle(sc.nextLine());
                }
                case 3 -> {
                    System.out.print("Enter author keyword: ");
                    searchByAuthor(sc.nextLine());
                }
                case 4 -> addBook(sc);
                case 5 -> deleteBook(sc);
                case 6 -> updateBook(sc);
                case 7 -> sortBooksByTitle();
                case 8 -> showStatistics();
                case 9 -> {
                    saveBooks();
                    System.out.println("Saved. Exiting...");
                }
                default -> System.out.println("Invalid choice.");
            }
        } while (choice != 9);

        sc.close();
    }

    private static void loadBooks() {
        File file = new File(CSV_FILE);
        if (!file.exists()) {
            System.out.println("No existing data found.");
            return;
        }
        try (BufferedReader br = new BufferedReader(new FileReader(file))) {
            String line;
            while ((line = br.readLine()) != null) {
                String[] data = line.split(",");
                if (data.length >= 5) {
                    books.add(new Book(data[0], data[1], data[2], data[3], data[4]));
                }
            }
            System.out.println("Loaded " + books.size() + " books from file.");
        } catch (IOException e) {
            System.out.println("Error loading file: " + e.getMessage());
        }
    }

    private static void displayBooks() {
        if (books.isEmpty()) {
            System.out.println("No books available.");
            return;
        }
        System.out.printf("%-12s %-40s %-25s %-15s %-6s%n", "ID", "Title", "Author", "Genre", "Year");
        System.out.println("----------------------------------------------------------------------------------------------------");
        for (Book b : books) {
            System.out.printf("%-12s %-40s %-25s %-15s %-6s%n",
                    b.getId(), b.getTitle(), b.getAuthor(), b.getGenre(), b.getYear());
        }
    }
    private static void searchByTitle(String keyword) {
        boolean found = false;
        for (Book b : books) {
            if (b.getTitle().toLowerCase().contains(keyword.toLowerCase())) {
                System.out.println(b);
                found = true;
            }
        }
        if (!found) System.out.println("No books found with that title.");
    }

    private static void searchByAuthor(String keyword) {
        boolean found = false;
        for (Book b : books) {
            if (b.getAuthor().toLowerCase().contains(keyword.toLowerCase())) {
                System.out.println(b);
                found = true;
            }
        }
        if (!found) System.out.println("No books found with that author.");
    }

    private static void addBook(Scanner sc) {
        System.out.print("Enter Book ID: ");
        String id = sc.nextLine();
        // Prevent duplicate ID
        for (Book b : books) {
            if (b.getId().equalsIgnoreCase(id)) {
                System.out.println("Book ID already exists. Cannot add.");
                return;
            }
        }
        System.out.print("Enter Title: ");
        String title = sc.nextLine();
        System.out.print("Enter Author: ");
        String author = sc.nextLine();
        System.out.print("Enter Genre: ");
        String genre = sc.nextLine();
        System.out.print("Enter Year: ");
        String year = sc.nextLine();

        books.add(new Book(id, title, author, genre, year));
        System.out.println("Book added successfully.");
    }

    private static void deleteBook(Scanner sc) {
        System.out.print("Enter Book ID to delete: ");
        String id = sc.nextLine();
        boolean removed = books.removeIf(b -> b.getId().equalsIgnoreCase(id));
        if (removed) {
            System.out.println("Book deleted successfully.");
        } else {
            System.out.println("Book not found.");
        }
    }

    
    private static void updateBook(Scanner sc) {
        System.out.print("Enter Book ID to update: ");
        String id = sc.nextLine();
        for (int i = 0; i < books.size(); i++) {
            Book b = books.get(i);
            if (b.getId().equalsIgnoreCase(id)) {
                System.out.print("Enter new title (leave blank to keep current): ");
                String title = sc.nextLine();
                if (title.isEmpty()) title = b.getTitle();

                System.out.print("Enter new author (leave blank to keep current): ");
                String author = sc.nextLine();
                if (author.isEmpty()) author = b.getAuthor();

                System.out.print("Enter new genre (leave blank to keep current): ");
                String genre = sc.nextLine();
                if (genre.isEmpty()) genre = b.getGenre();

                System.out.print("Enter new year (leave blank to keep current): ");
                String year = sc.nextLine();
                if (year.isEmpty()) year = b.getYear();

                books.set(i, new Book(id, title, author, genre, year));
                System.out.println("Book updated successfully.");
                return;
            }
        }
        System.out.println("Book not found.");
    }

   
    private static void sortBooksByTitle() {
        books.sort(Comparator.comparing(Book::getTitle));
        System.out.println("Books sorted by title.");
    }

   
    private static void showStatistics() {
        System.out.println("Total books: " + books.size());
        Map<String, Long> genreCount = books.stream()
                .collect(Collectors.groupingBy(Book::getGenre, Collectors.counting()));
        genreCount.forEach((genre, count) ->
                System.out.println(genre + ": " + count + " books"));
    }

    private static void saveBooks() {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(CSV_FILE))) {
            for (Book b : books) {
                bw.write(b.toCSV());
                bw.newLine();
            }
        } catch (IOException e) {
            System.out.println("Error saving file: " + e.getMessage());
        }
    }
}

SERVICEAPI

package com.example.restapi;

import com.sun.net.httpserver.HttpServer;

import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.InetSocketAddress;
import java.util.Arrays;
import java.util.concurrent.Executors;

public class ServiceTwo {
    private final int port;
    public ServiceTwo(int port) {
        this.port = port;
    }
    public void start() throws IOException {
        HttpServer server =HttpServer.create(new InetSocketAddress(port), 0);
        server.createContext("/sum", exchange -> {
            try (InputStream is = exchange.getRequestBody();
                 OutputStream os = exchange.getResponseBody()) {
                String data =new String(is.readAllBytes());

                long sum = Arrays.stream(data.replaceAll("[^0-9,-]","" ).split(","))
                        .filter(s-> !s.isEmpty())
                        .mapToLong(Long::parseLong)
                        .sum();

                byte[] response = String.valueOf(sum).getBytes();
                exchange.sendResponseHeaders(200,response.length);
                os.write(response);}});
        server.setExecutor(Executors.newVirtualThreadPerTaskExecutor());
        server.start();

            }
        }
        
      package com.example.restapi;

import com.sun.net.httpserver.HttpServer;

import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.InetSocketAddress;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.time.Duration;
import java.util.concurrent.Executors;

public class ServiceOne {
    private int port;
    private final String serviceTwo;
    private final HttpClient httpClient;

    public ServiceOne(int port, String serviceTwo) {
        this.port = port;
        this.serviceTwo = serviceTwo;
        this.httpClient = HttpClient.newBuilder().connectTimeout(Duration.ofSeconds(2)).build();
    }
    public void start() throws IOException {

        HttpServer server = HttpServer.create(new InetSocketAddress(port), 0);

        server.createContext("/calculate", exchange -> {
            try (InputStream is = exchange.getRequestBody();
                 OutputStream os = exchange.getResponseBody()) {

                HttpRequest request = HttpRequest.newBuilder(URI.create(serviceTwo + "/sum"))
                        .POST(HttpRequest.BodyPublishers.ofInputStream(() -> is))
                        .timeout(Duration.ofSeconds(3))
                        .build();

                HttpResponse response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
                byte[] finalResponse = ("totalSum" + response.body()).getBytes();
                exchange.sendResponseHeaders(200, finalResponse.length);
                os.write(finalResponse);

            } catch (Exception e) {
                exchange.sendResponseHeaders(500, 0);
            }
        });
        server.setExecutor(Executors.newVirtualThreadPerTaskExecutor());
        server.start();
    }}
    
    package com.example.restapi;

import java.io.IOException;

public class MainApplication {
    public static void main(String[] args) throws IOException {

        new ServiceTwo(8082).start();
        new ServiceOne(8081, "http://localhost:8082").start();
        System.out.println("Application started.");
    }
}



----------------------------
memoryleak
package org.example;

import java.io.BufferedOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Random;

public class MemoryLeakDemo {

    public static void main(String[] args) {
        Random random = new Random();

        int size = 1024 * 1024;
        byte[] buffer = new byte[size];
        try (BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("myFile.txt"))) {
            for (int i = 0; i < 10000; i++) {
                random.nextBytes(buffer);

                bos.write(buffer);
                System.out.println("Added block " + (i + 1));
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
            System.out.println("Finished adding data blocks.");
        } catch (IOException e) {
            System.out.println("MemoryLeakDemo.main" + e.getMessage());
        }
    }
}

---------------------
arrayexception
package org.example;

import java.util.Arrays;

public class IntegerArrayProcessor {
    public int sumOfIntegers(String[] numbers) throws ArrayProcessoorException {
        if (numbers == null) {
            throw new ArrayProcessoorException("can not be null");
        }
        try {
            return Arrays.stream(numbers).mapToInt(this::safetltParseInt)
                    .reduce(0, Math::addExact);
        } catch (ArithmeticException e) {
            throw new ArrayProcessoorException("integer overflow" + e);
        } catch (Exception e) {
            throw new ArrayProcessoorException("failed", e);
        }
    }

    private int safetltParseInt(String num) {
        if (num == null)  {
            return 0;
        }
        try {
            return Integer.parseInt(num.trim());
        } catch (NumberFormatException e) {
            System.out.printf("inavalid data \n", num);
            return 0;
        }
    }
}
---------------------------------
strategy pattern
package com.example;

import com.example.StringOperation;
import com.example.StringOperationEnum;

import java.util.Scanner;

public class StringUtilityApp {
    public static void main(String[] args) {
        // باز کردن اسکنر در بلاک try-with-resources برای جلوگیری از نشت حافظه
        try (Scanner scanner = new Scanner(System.in)) {
            System.out.println("Enter a string:");
            String input = scanner.nextLine();

            displayDynamicMenu();

            // برنامه‌نویسی تدافعی: جلوگیری از کرش کردن برنامه در صورت ورود حروف به جای عدد
            if (scanner.hasNextInt()) {
                int choice = scanner.nextInt();
                processChoice(choice, input);
            } else {
                System.out.println("Invalid input. Please enter a number.");
            }
        }
    }

    // ⭐ ترفند سنیور: ساخت منو به صورت داینامیک. 
    // اضافه کردن قابلیت جدید فقط با تغییر Enum انجام می‌شود و این متد دست نمی‌خورد.
    private static void displayDynamicMenu() {
        System.out.print("Choose operation: ");
        StringOperationEnum[] operations = StringOperationEnum.values();
        for (int i = 0; i < operations.length; i++) {
            System.out.printf("%d) %s ", (i + 1), operations[i].getDescription());
        }
        System.out.println();
    }

    private static void processChoice(int choice, String input) {
        StringOperationEnum[] operations = StringOperationEnum.values();

        // پیدا کردن استراتژی بر اساس ایندکس (بدون نیاز به filter و findFirst)
        if (choice > 0 && choice <= operations.length) {
            StringOperation operation = operations[choice - 1].getOperation();
            System.out.println(operation.operation(input));
        } else {
            System.out.println("Invalid choice");
        }
    }
    package com.example;

public enum StringOperationEnum {
    REVERSE("reverse", new ReversedOperation()),
    UPPER_CASE("uppercase", new UppercaseOperation()),
    COUNT_VOWELS("countVowels", new CountVowelsOperation());

    private final String description;
    private final StringOperation operation;
    

    StringOperationEnum(String description, StringOperation operation) {
        this.description = description;
        this.operation = operation;
    }

    public String getDescription() {
        return description;
    }

    public StringOperation getOperation() {
        return operation;
    }

}package com.example;

public class CountVowelsOperation implements StringOperation {
    @Override
    public String operation(String value) {
        long count = value.toLowerCase().chars()
                .filter(c -> c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u').count();
     return String.valueOf(count);
    }
}
-------------------
dependency injection
package com.example.di;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;

public class SimpleInjector {

    private final static Map<Class<?>, Class<?>> REGISTERY = new ConcurrentHashMap<>();

    static {
        REGISTERY.put(MessageService.class, EmailMessageService.class);
    }

    public static <T> T getInstance(Class<T> clazz) {
        return getInstance(clazz, new HashSet<>());
    }


    @SuppressWarnings("unchecked")
    public static <T> T getInstance(Class<T> clazz, Set<Class<?>> resolving) {
        Class<?> target = REGISTERY.getOrDefault(clazz, clazz);

        if (!resolving.add(target)) {
            throw new RuntimeException("" + target.getName());
        }

        try {
            Constructor<?> constructor = target.getConstructors()[0];
            Class<?>[] parmTypes = constructor.getParameterTypes();
            Object[] args = new Object[parmTypes.length];

            for (int i = 0; i < parmTypes.length; i++) {
                args[i] = getInstance(parmTypes[i], resolving);
            }

            T instance = (T) constructor.newInstance(args);
            resolving.remove(target);
            return instance;
        } catch (Exception e) {
            throw new RuntimeException("SimpleInjector.getInstance"+ target.getName(), e);
        }
    }
}
-------
dependencyinject
public class ServiceConsumer {
    

    private final Service service;

    public ServiceConsumer(Service service) {
        // ⭐ ترفند سنیور ۲: برنامه‌نویسی تدافعی (Fail-Fast)
        // در سیستم‌های بدون فریم‌ورک، باید مطمئن شویم کسی به اشتباه مقدار null تزریق نمی‌کند.
        this.service = Objects.requireNonNull(service, "Service dependency cannot be null");
    }

    public void process() {
        service.execute();
    }
}


Java

package com.example.di;

public class Main {
    public static void main(String[] args) {
      
        Service service = new ServiceImpl(); 
        
        ServiceConsumer consumer = new ServiceConsumer(service);
        consumer.process();
    }
}


}
  ----------------
  upgradabilityConfig
  package com.example.UpgradeabilityConfigurableApp;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;
import java.util.Objects;
import java.util.Properties;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.ServiceLoader;

public class Main {
    public static void main(String[] args) {
        Properties config = new Properties();
        try (FileInputStream fis = new FileInputStream("config.properties")) {
            config.load(fis);
        } catch (IOException e) {
            System.out.println("Failed to load config.properties");
            return;
        }


        String version = config.getProperty("version");
        if (version == null) {
            System.out.println("No version specified in config");
            return;
        }

        ServiceLoader<VersionedConfigHandler> loader = ServiceLoader.load(VersionedConfigHandler.class);
        for (VersionedConfigHandler handler : loader) {
            if (handler.supportsVersion(version)) {
                handler.handle(config);
                return;
            }
        }


    }
}
  public interface VersionedConfigHandler {
    boolean supportsVersion(String version);
    void handle(Properties config);
}package com.example.UpgradeabilityConfigurableApp;

import java.util.Properties;

public class Version2ConfigHandler implements VersionedConfigHandler {
    @Override
    public boolean supportsVersion(String version) {
        return "2".equals(version);
    }

    @Override
    public void handle(Properties config) {
        System.out.println("Running version 2 logic");
        System.out.println("Welcome, " + config.getProperty("username"));
        System.out.println("Your role is: " + config.getProperty("role"));
    }
}package com.example.UpgradeabilityConfigurableApp;

import java.util.Properties;

public class Version1ConfigHandler implements VersionedConfigHandler {
    @Override
    public boolean supportsVersion(String version) {
        return "1".equals(version);
    }

    @Override
    public void handle(Properties config) {
        System.out.println("Running version 1 logic");
        System.out.println("Welcome, " + config.getProperty("username"));
    }
}

public class Version1ConfigHandler implements VersionedConfigHandler {
    @Override
    public boolean supportsVersion(String version) {
        return "1".equals(version);
    }

    @Override
    public void handle(Properties config) {
        System.out.println("Running version 1 logic");
        System.out.println("Welcome, " + config.getProperty("username"));
    }
}

public class Version1ConfigHandler implements VersionedConfigHandler {
    @Override
    public boolean supportsVersion(String version) {
        return "1".equals(version);
    }

    @Override
    public void handle(Properties config) {
        System.out.println("Running version 1 logic");
        System.out.println("Welcome, " + config.getProperty("username"));
    }
}

-------------------
troubleshooting app



public class TroubleshootingApp {
    
    public static void main(String[] args) {
     
        try (Scanner scanner = new Scanner(System.in)) {
            List<String> items = new ArrayList<>();

            System.out.println("Enter number of items:");
            
          
            if (!scanner.hasNextInt()) {
                System.err.println("Error: Invalid input. Expected an integer.");
                return;
            }
            int n = scanner.nextInt();
            scanner.nextLine(); // خالی کردن بافر

         
            for (int i = 0; i < n; i++) {
                System.out.println("Enter item " + (i + 1) + ":");
                items.add(scanner.nextLine()); // کد تمیزتر (بدون نیاز به متغیر اضافی)
            }

            System.out.println("All items in uppercase:");
           
            items.stream()
                 .map(String::toUpperCase)
                 .forEach(System.out::println);

           
            if (items.isEmpty()) {
                System.out.println("List is empty. Exiting...");
                return;
            }
            
            System.out.printf("Enter index to remove (0 to %d):%n", items.size() - 1);
            if (scanner.hasNextInt()) {
                int idx = scanner.nextInt();

              
                if (idx >= 0 && idx < items.size()) {
                    String removedItem = items.remove(idx);
                    System.out.println("Successfully removed: " + removedItem);
                } else {
                    System.err.println("Error: Index out of bounds.");
                }
            } else {
                System.err.println("Error: Invalid index format.");
            }

            System.out.println("Remaining items:");
           
            items.forEach(System.out::println);
        }
    }
}
---------
levergability
package com.example.leverageability;

public interface Product {
    
    // ۱. متدی که هر محصول جدید باید پسوند اختصاصی خودش را برای آن برگرداند
    String getSuffix();

    // ۲. منطق مشترک (Leveraged Logic) که در تمام محصولات ثابت است
    default String process(String input) {
        // برنامه‌نویسی تدافعی (Defensive Programming): مدیریت مقادیر نال یا خالی
        if (input == null || input.trim().isEmpty()) {
            return getSuffix();
        }
        
        String normalized = input.trim().toLowerCase();
        String reversed = new StringBuilder(normalized).reverse().toString();
        
        return reversed + getSuffix();
    }
}

-------------------------------
commandProcessor
package com.example;

import java.util.Map;
import java.util.Scanner;
import java.util.ServiceLoader;
import java.util.stream.Collectors;

public class CommandProcessor {
    public static void main(String[] args) {
        Map<String, Command> registry = ServiceLoader.load(Command.class)
                .stream()
                .map(ServiceLoader.Provider::get)
                .collect(Collectors.toMap(c -> c.getName().toLowerCase(), c -> c));

        try (Scanner scanner = new Scanner(System.in)) {
            System.out.println("System ready. Enter commands:");

            while (scanner.hasNextLine()) {
                String input = scanner.nextLine().trim();

                if (input.equalsIgnoreCase("exit")) {
                    System.out.println("Exiting...");
                    break;
                }
                if (input.isEmpty()) continue;

                // ⭐ برنامه‌نویسی تدافعی: جداسازی ایمن نام دستور و آرگومان‌ها
                // اسپلیت کردن رشته فقط به 2 بخش، جایگزین بسیار امن‌تر برای substring
                String[] parts = input.split(" ", 2);
                String cmdName = parts[0].toLowerCase();
                String argument = parts.length > 1 ? parts[1] : "";

                // یافتن و اجرای دستور (الگوی Command)
                Command command = registry.get(cmdName);
                if (command != null) {
                    command.processCommand(argument);
                } else {
                    System.err.println("Unknown command: " + cmdName);
                }
            }
        }
    }
}

-----
supportabilityconsoleapp
package com.example.SupportabilityConsoleApp;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;
import java.util.logging.Level;
import java.util.logging.Logger;

public class Main {
    private static final List<String> operations = new ArrayList<>();
    private static final Logger logger = Logger.getLogger(Main.class.getName());


    public static void main(String[] args) {
        try (Scanner scanner = new Scanner(System.in)) {
            boolean running = true;

            while (running) {
                System.out.println("\nChoose operation: 1-Add 2-List 3-Remove 4-Exit");
                String input = scanner.nextLine().trim();

                // ⭐ استفاده از Switch Expression جاوا 21 (بسیار تمیزتر از if-else های تودرتو)
                switch (input) {
                    case "1" -> addOperation(scanner);
                    case "2" -> listOperations();
                    case "3" -> removeOperation(scanner);
                    case "4" -> {
                        logger.info("Application exited normally by user.");
                        System.out.println("Goodbye!");
                        running = false;
                    }
                    default -> {
                        // لاگ کردن رفتار اشتباه کاربر برای تیم پشتیبانی
                        logger.warning("Invalid menu option selected by user: [" + input + "]");
                        System.out.println("Invalid option. Please enter a number between 1 and 4.");
                    }
                }
            }
        } catch (Exception e) {
            // ⭐ ثبت بالاترین سطح خطا در صورت بروز مشکل غیرمنتظره و کرش کردن برنامه
            logger.log(Level.SEVERE, "Unexpected fatal error occurred in the application loop.", e);
        }
    }

    private static void addOperation(Scanner scanner) {
        System.out.println("Enter operation to add:");
        String op = scanner.nextLine().trim();

        if (op.isEmpty()) {
            logger.warning("User attempted to add an empty operation.");
            System.out.println("Operation cannot be empty!");
            return;
        }

        operations.add(op);
        logger.info("Operation added successfully: [" + op + "]");
        System.out.println("Added.");
    }

    private static void removeOperation(Scanner scanner) {
        System.out.println("Enter index to remove:");
        String input = scanner.nextLine().trim();

        try {
            int idx = Integer.parseInt(input);

            // ⭐ اعتبارسنجی دستی مرزها (Bounds Checking) به جای پرتاب شدن IndexOutOfBoundsException
            if (idx >= 0 && idx < operations.size()) {
                String removedOp = operations.remove(idx);
                logger.info("Operation removed at index " + idx + ": [" + removedOp + "]");
                System.out.println("Removed successfully.");
            } else {
                logger.warning("User provided out-of-bounds index: " + idx + ". Valid range: 0 to " + (operations.size() - 1));
                System.out.println("Error: Index out of range.");
            }
        } catch (NumberFormatException e) {
            // جلوگیری از کرش کردن برنامه در صورت ورود حروف به جای عدد
            logger.warning("Invalid numeric input for removal: [" + input + "]");
            System.out.println("Error: Please enter a valid number.");
        }
    }

    private static void listOperations() {
        logger.info("User requested to list operations. Current size: " + operations.size());

        if (operations.isEmpty()) {
            System.out.println("The list is empty.");
            return;
        }
        for (int i = 0; i < operations.size(); i++) {
            System.out.println(i + ": " + operations.get(i)); // UI برای کاربر
        }
    }
}

-----------
performanceTunningarrayprocessing


public class PerformanceTuningArrayProcessing {

    public static void main(String[] args) {
        int size = 10_000_000;
        
      
        int[] numbers = new int[size];
        Random random = new Random(42);
        for (int i = 0; i < size; i++) {
            numbers[i] = random.nextInt(1_000_000);
        }

        long start = System.currentTimeMillis();

      
        long sum = Arrays.stream(numbers)
                .parallel()
                .filter(PerformanceTuningArrayProcessing::isPrime)
                .asLongStream()
                .sum();

        long end = System.currentTimeMillis();
        System.out.println("Sum of prime numbers: " + sum);
        System.out.println("Time elapsed: " + (end - start) + " ms");
    }

   
    public static boolean isPrime(int n) {
      
        if (n <= 1) return false;
      
        if (n <= 3) return true;
        if (n % 2 == 0 || n % 3 == 0) return false;

       
        for (int i = 5; i * i <= n; i += 6) {
            if (n % i == 0 || n % (i + 2) == 0) return false;
        }
        return true;
    }
}
-------------------------
  singleton
  package com.patternorchestrator;

public class SingletonOrchestrator {
    private final OperationManager manager;

    private SingletonOrchestrator() {
        this.manager = new OperationManager();
    }

    public static  class InstanceHolder {
        private static final SingletonOrchestrator INSTANCE = new SingletonOrchestrator();
    }

    public static SingletonOrchestrator getInstance() {
        return InstanceHolder.INSTANCE;
    }

    public OperationManager getManager() {
        return manager;
    }
}
package com.patternorchestrator;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

public class OperationManager {
    private  final List<Operation> operations = new CopyOnWriteArrayList<>();

    public OperationManager addOperation(Operation operation) {
        this.operations.add(operation);
    return  this;}

    public void executeAll() {
operations.forEach(Operation::execute);

    }
}package com.patternorchestrator;

import java.util.Objects;

public class OperationFactory {
    public Operation createOperation(String type, String value) {
        Objects.requireNonNull(value);
        Objects.requireNonNull(type);

        return switch (type.toLowerCase()) {
            case "print" -> new PrintOperation(value);
            default -> throw new IllegalStateException("Unexpected value: " + type.toLowerCase());
        };
    }
}package com.patternorchestrator;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

public class OperationManager {
    private  final List<Operation> operations = new CopyOnWriteArrayList<>();

    public OperationManager addOperation(Operation operation) {
        this.operations.add(operation);
    return  this;}

    public void executeAll() {
operations.forEach(Operation::execute);

    }
}package com.patternorchestrator;

public class Main {
    public static void main(String[] args) {
        OperationFactory factory = new OperationFactory();
        SingletonOrchestrator orchestrator = SingletonOrchestrator.getInstance();
        OperationManager manager = orchestrator.getManager();

        Operation op1 = factory.createOperation("print", "Hello");
        Operation op2 = factory.createOperation("print", "Design Patterns");

        manager.addOperation(op1).addOperation(op2);

        manager.executeAll();
    }
}

-------------
upgradabilityappmodule
public class UpgradeabilityApp {
    public static void main(String[] args) {
        UpgradeManager manager = new UpgradeManager();
        
        // مدیریت ایمن منابع (بسته شدن خودکار اسکنر در صورت خروج)
        try (Scanner scanner = new Scanner(System.in)) {
            while (true) {
                System.out.println("\n1. Register module\n2. List modules\n3. Upgrade module\n4. Exit");
                String choice = scanner.nextLine().trim();
                
                try {
                    switch (choice) {
                        case "1" -> {
                            System.out.print("Module name: ");
                            String name = scanner.nextLine().trim();
                            System.out.print("Version (e.g. 1.0.0): ");
                            String version = scanner.nextLine().trim();
                            System.out.print("Is server? (y/n): ");
                            boolean isServer = scanner.nextLine().trim().equalsIgnoreCase("y");
                            
                            manager.registerModule(name, version, isServer);
                            System.out.println("Module registered successfully.");
                        }
                        case "2" -> {
                            if (manager.getModules().isEmpty()) {
                                System.out.println("No modules registered yet.");
                            } else {
                                manager.getModules().forEach(System.out::println);
                            }
                        }
                        case "3" -> {
                            System.out.print("Module name: ");
                            String name = scanner.nextLine().trim();
                            System.out.print("New version: ");
                            String version = scanner.nextLine().trim();
                            System.out.print("Is server? (y/n): ");
                            // ⭐ رفع باگ شما: اینجا متغیر isServer باید از کاربر گرفته و پاس داده شود
                            boolean isServer = scanner.nextLine().trim().equalsIgnoreCase("y");
                            
                            if (manager.upgradeModule(name, version, isServer)) {
                                System.out.println("Upgraded successfully.");
                            } else {
                                System.out.println("Upgrade failed. Module not found or new version is not higher.");
                            }
                        }
                        case "4" -> {
                            System.out.println("Exiting...");
                            return;
                        }
                        default -> System.out.println("Invalid option. Please enter 1-4.");
                    }
                } catch (IllegalArgumentException e) {
                    // در صورت ورود فرمت اشتباه نسخه، برنامه کرش نمی‌کند بلکه پیام خطا می‌دهد
                    System.err.println("Error: " + e.getMessage());
                }
            }
        }
    }
}public class ModuleVersion {
    private final String moduleName;
    private String version;
    private final boolean isServer;

    public ModuleVersion(String moduleName, String version, boolean isServer) {
        this.moduleName = moduleName;
        this.version = version;
        this.isServer = isServer;
    }

    public String getModuleName() { return moduleName; }
    public String getVersion() { return version; }
    public boolean isServer() { return isServer; }
    public void setVersion(String version) { this.version = version; }

    @Override
    public String toString() {
        return moduleName + " (" + (isServer ? "Server" : "Client") + ") - Version: " + version;
    }
}
-------------------

maintabilityrefactor
public class ItemManager {
    private final List<String> items = new ArrayList<>();

    public void addItem(String item) {
        if (item == null || item.trim().isEmpty()) {
            throw new IllegalArgumentException("Item cannot be empty.");
        }
        items.add(item.trim());
    }

    public boolean removeItem(String item) {
        if (item == null) return false;
        return items.remove(item.trim());
    }

    public List<String> getItems() {
        // بازگرداندن یک لیست غیرقابل تغییر برای حفظ Immutability
        return Collections.unmodifiableList(items);
    }
}@FunctionalInterface
public interface Command {
    void execute(ItemManager manager, Scanner scanner);
}
public enum MenuOption {
    ADD(1, "Add item", (manager, scanner) -> {
        System.out.print("Enter item: ");
        try {
            manager.addItem(scanner.nextLine());
            System.out.println("Item added successfully.");
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }),
    
    REMOVE(2, "Remove item", (manager, scanner) -> {
        System.out.print("Enter item to remove: ");
        if (manager.removeItem(scanner.nextLine())) {
            System.out.println("Item removed successfully.");
        } else {
            System.out.println("Item not found.");
        }
    }),
    
    LIST(3, "List items", (manager, scanner) -> {
        var items = manager.getItems();
        if (items.isEmpty()) {
            System.out.println("No items available.");
        } else {
            System.out.println("Items:");
            items.forEach(item -> System.out.println("- " + item));
        }
    }),
    
    EXIT(4, "Exit", (manager, scanner) -> {
        System.out.println("Goodbye!");
        System.exit(0);
    });

    private final int code;
    private final String description;
    private final Command command;

    MenuOption(int code, String description, Command command) {
        this.code = code;
        this.description = description;
        this.command = command;
    }

    public int getCode() { return code; }
    public String getDescription() { return description; }
    public Command getCommand() { return command; }

    // متد کمکی برای یافتن دستور از روی کد ورودی
    public static MenuOption fromCode(int code) {
        for (MenuOption option : values()) {
            if (option.getCode() == code) return option;
        }
        return null;
    }
}public class ConsoleApp {
    private final ItemManager itemManager;

    public ConsoleApp(ItemManager itemManager) {
        this.itemManager = itemManager;
    }

    public void start() {
        try (Scanner scanner = new Scanner(System.in)) {
            while (true) {
                displayMenu();
                System.out.print("Choose an option: ");
                
                try {
                    int optionCode = Integer.parseInt(scanner.nextLine().trim());
                    MenuOption selectedOption = MenuOption.fromCode(optionCode);
                    
                    if (selectedOption != null) {
                        selectedOption.getCommand().execute(itemManager, scanner);
                    } else {
                        System.out.println("Invalid option. Please select a valid number.");
                    }
                } catch (NumberFormatException e) {
                    System.out.println("Error: Please enter a numeric value.");
                }
                System.out.println(); // خط خالی برای خوانایی بهتر
            }
        }
    }

    private void displayMenu() {
        System.out.println("=== Item Manager Menu ===");
        for (MenuOption option : MenuOption.values()) {
            System.out.printf("%d. %s%n", option.getCode(), option.getDescription());
        }
    }
}


Java

package com.example;

import com.example.cli.ConsoleApp;
import com.example.core.ItemManager;

public class Main {
    public static void main(String[] args) {
        // تزریق وابستگی (Dependency Injection)
        ItemManager manager = new ItemManager();
        ConsoleApp app = new ConsoleApp(manager);
        
        // شروع برنامه
        app.start();
    }
}

    ------------------------
    
    reliability
    
    
    package com.example.reliabilitychecker;

public class NonCriticalException extends ServiceException {
    public NonCriticalException(String message) {
        super(message);
    }
}


Service.java:
Java

package com.example.reliabilitychecker;

public interface Service {
    String getName();
    boolean isCritical();
    

    boolean checkHealth() throws ServiceException; 
}

CriticalService.java:
Java

package com.example.reliabilitychecker;

public class CriticalService implements Service {
    private final String name;

    public CriticalService(String name) {
        this.name = name;
    }

    @Override
    public String getName() { return name; }

    @Override
    public boolean isCritical() { return true; }

    @Override
    public boolean checkHealth() throws CriticalException {
        if (Math.random() < 0.5) {
            throw new CriticalException("Critical Service Failure: " + name);
        }
        return true;
    }
}

NonCriticalService.java:
Java

package com.example.reliabilitychecker;

public class NonCriticalService implements Service {
    private final String name;

    public NonCriticalService(String name) {
        this.name = name;
    }

    @Override
    public String getName() { return name; }

    @Override
    public boolean isCritical() { return false; }

    @Override
    public boolean checkHealth() throws NonCriticalException {
        if (Math.random() < 0.5) {
            throw new NonCriticalException("Non-Critical Service Failure: " + name);
        }
        return true;
    }
}


SystemHealthChecker.java:
Java

package com.example.reliabilitychecker;

import java.util.List;

public class SystemHealthChecker {
    private final List<Service> services;

    public SystemHealthChecker(List<Service> services) {
        this.services = services;
    }

    public void checkAll() {
        System.out.println("--- Starting System Health Check ---");
        
        for (Service service : services) {
            try {
                service.checkHealth();
                System.out.println("✅ " + service.getName() + " is HEALTHY.");
            } catch (CriticalException e) {
                // خطای بحرانی: بلافاصله برنامه را متوقف می‌کنیم
                System.err.println("❌ FATAL: " + e.getMessage());
                System.err.println("System shutting down due to critical failure.");
                System.exit(1); // خروج با کد خطا
            } catch (NonCriticalException e) {
                // خطای غیر بحرانی: فقط لاگ می‌کنیم و ادامه می‌دهیم
                System.out.println("⚠️ WARNING: " + e.getMessage() + ". System will continue running.");
            } catch (ServiceException e) {
                // مدیریت هرگونه خطای پیش‌بینی نشده در سطح سرویس
                System.err.println("❌ Unknown Service Error: " + e.getMessage());
            }
        }
--------------

secureuseconsole


package com.example.SecureUserConsoleApp;

import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.PBEKeySpec;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
import java.security.spec.InvalidKeySpecException;
import java.security.spec.KeySpec;
import java.sql.*;
import java.util.Base64;
import java.util.Scanner;
import java.util.logging.Level;
import java.util.logging.Logger;

public class Main {
    private static final Logger logger = Logger.getLogger(Main.class.getName());
    
    // ⭐ ترفند سنیور: ثابت نگه داشتن دیتابیس H2 در حافظه با DB_CLOSE_DELAY
    private static final String DB_URL = "jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1";
    
    // تنظیمات استاندارد PBKDF2 برای سال 2026
    private static final int ITERATIONS = 100_000;
    private static final int KEY_LENGTH = 256;
    private static final String ALGORITHM = "PBKDF2WithHmacSHA256";

    public static void main(String[] args) {
        initDatabase();

        // مدیریت ایمن منابع (try-with-resources)
        try (Scanner scanner = new Scanner(System.in)) {
            while (true) {
                System.out.println("\n--- Secure Authentication System ---");
                System.out.println("1. Register\n2. Login\n3. Exit");
                System.out.print("Choose an option: ");
                
                String choice = scanner.nextLine().trim();

                switch (choice) {
                    case "1" -> registerUser(scanner);
                    case "2" -> loginUser(scanner);
                    case "3" -> {
                        System.out.println("Exiting System...");
                        return;
                    }
                    default -> System.out.println("Invalid option. Try again.");
                }
            }
        }
    }

    private static void initDatabase() {
        // ⭐ ترفند سنیور: استفاده از Text Blocks (جاوا 15+) برای خوانایی کوئری‌ها
        String sql = """
            CREATE TABLE IF NOT EXISTS users (
                id INT AUTO_INCREMENT PRIMARY KEY,
                username VARCHAR(255) UNIQUE NOT NULL,
                password_hash VARCHAR(255) NOT NULL,
                salt VARCHAR(255) NOT NULL
            )
        """;
        try (Connection conn = DriverManager.getConnection(DB_URL, "sa", "");
             Statement stmt = conn.createStatement()) {
            stmt.execute(sql);
        } catch (SQLException e) {
            logger.log(Level.SEVERE, "Database initialization failed.", e);
            System.exit(1);
        }
    }

    private static void registerUser(Scanner scanner) {
        System.out.print("Username: ");
        String username = scanner.nextLine().trim();
        System.out.print("Password: ");
        String password = scanner.nextLine();

        if (username.isEmpty() || password.isEmpty()) {
            System.out.println("Username and password cannot be empty.");
            return;
        }

        try (Connection conn = DriverManager.getConnection(DB_URL, "sa", "")) {
            // تولید نمک تصادفی واقعی (SecureRandom)
            byte[] saltBytes = generateSalt();
            String saltBase64 = Base64.getEncoder().encodeToString(saltBytes);
            String hashBase64 = hashPassword(password, saltBytes);

            String sql = "INSERT INTO users (username, password_hash, salt) VALUES (?, ?, ?)";
            try (PreparedStatement ps = conn.prepareStatement(sql)) {
                ps.setString(1, username);
                ps.setString(2, hashBase64);
                ps.setString(3, saltBase64);
                ps.executeUpdate();
                System.out.println("✅ Registration successful!");
            }
        } catch (SQLException e) {
            // جلوگیری از ثبت‌نام کاربر تکراری (مدیریت خطای UNIQUE)
            if (e.getErrorCode() == 23505) { 
                System.out.println("❌ Registration failed: Username already exists.");
            } else {
                logger.log(Level.WARNING, "Database error during registration", e);
            }
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Cryptography error", e);
        }
    }

    private static void loginUser(Scanner scanner) {
        System.out.print("Username: ");
        String username = scanner.nextLine().trim();
        System.out.print("Password: ");
        String password = scanner.nextLine();

        String sql = "SELECT password_hash, salt FROM users WHERE username = ?";

        try (Connection conn = DriverManager.getConnection(DB_URL, "sa", "");
             PreparedStatement ps = conn.prepareStatement(sql)) {

            ps.setString(1, username);
            try (ResultSet rs = ps.executeQuery()) {
                if (rs.next()) {
                    String storedHash = rs.getString("password_hash");
                    String storedSaltBase64 = rs.getString("salt");

                    // تبدیل نمک ذخیره شده به بایت و هش کردن رمز عبور ورودی با همان نمک
                    byte[] saltBytes = Base64.getDecoder().decode(storedSaltBase64);
                    String computedHash = hashPassword(password, saltBytes);

                    // ⭐ ترفند امنیتی سنیور: استفاده از MessageDigest.isEqual برای جلوگیری از Timing Attack
                    if (MessageDigest.isEqual(storedHash.getBytes(), computedHash.getBytes())) {
                        System.out.println("✅ Login successful! Welcome, " + username + ".");
                    } else {
                        System.out.println("❌ Login failed: Incorrect password.");
                    }
                } else {
                    System.out.println("❌ Login failed: Username not found.");
                }
            }
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Login process error", e);
        }
    }

    // تولید نمک با امنیت رمزنگاری (Cryptographically Secure)
    private static byte[] generateSalt() {
        SecureRandom random = new SecureRandom();
        byte[] salt = new byte[16];
        random.nextBytes(salt);
        return salt;
    }

    private static String hashPassword(String password, byte[] salt) throws NoSuchAlgorithmException, InvalidKeySpecException {
        KeySpec spec = new PBEKeySpec(password.toCharArray(), salt, ITERATIONS, KEY_LENGTH);
        SecretKeyFactory factory = SecretKeyFactory.getInstance(ALGORITHM);
        byte[] hash = factory.generateSecret(spec).getEncoded();
        return Base64.getEncoder().encodeToString(hash);
    }
}
--------------------------------------------------------------
configurablesettingapp
package com.example.config;

import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class SettingsLoader {
    private final String configFilePath;
    
    // ⭐ ترفند سنیور: استفاده از volatile برای تضمین آپدیت شدن آنی رفرنس در تمام Threadها
    private volatile Properties properties;

    public SettingsLoader(String configFilePath) {
        this.configFilePath = configFilePath;
        this.properties = new Properties(); // جلوگیری از NullPointerException قبل از اولین لود
        load();
    }

    public void load() {
        // ایجاد یک نمونه کاملاً جدید برای لود کردن (جلوگیری از تغییر دیتای در حال استفاده)
        Properties newProperties = new Properties();
        
        try (InputStream input = new FileInputStream(configFilePath)) {
            newProperties.load(input);
            
            // ⭐ تعویض اتمیک (Atomic Swap): هیچ Thread ای دیتای نصفه‌کاره نمی‌بیند
            this.properties = newProperties;
            
        } catch (IOException e) {
            // برنامه‌نویسی تدافعی: اگر فایل خراب بود، برنامه کرش نمی‌کند و تنظیمات قبلی حفظ می‌شود
            System.err.println("Error reloading config: " + e.getMessage() + ". Retaining previous settings.");
        }
    }

    public String get(String key) {
        // خواندن بسیار سریع و بدون نیاز به Lock
        return properties.getProperty(key);
    }
}

۲. کلاس Main.java (رابط کاربری تمیز و مدرن)
Java

package com.example;

import com.example.config.SettingsLoader;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        String configFile = "application.properties";
        SettingsLoader loader = new SettingsLoader(configFile);

        // مدیریت ایمن منابع (try-with-resources)
        try (Scanner scanner = new Scanner(System.in)) {
            System.out.println("System initialized. Enter setting key, 'reload', or 'exit':");

            while (true) {
                System.out.print("> ");
                String input = scanner.nextLine().trim();

                if (input.isEmpty()) continue;

                // ⭐ استفاده از Switch Expression در جاوا مدرن
                switch (input.toLowerCase()) {
                    case "exit" -> {
                        System.out.println("Exiting application...");
                        return;
                    }
                    case "reload" -> {
                        loader.load();
                        System.out.println("✅ Configuration reloaded successfully.");
                    }
                    default -> {
                        String value = loader.get(input);
                        if (value == null) {
                            System.out.println("⚠️ Key '" + input + "' not found in configuration.");
                        } else {
                            System.out.println("🔹 " + input + " = " + value);
                        }
                    }
                }
            }
        }
    }
}


---------------------
componentlevergability

public interface Component {
    void execute();
}
۲. پیاده‌سازی جزء شترک


src/main/java/org/example/SharedComponent.java
Java

package org.example;

public class SharedComponent implements Component {
    @Override
    public void execute() {
        System.out.println("Shared component logic executed.");
    }
}



src/main/java/org/example/Product.java
Java

package org.example;

public interface Product {
    void run();
}


src/main/java/org/example/ProductA.java
Java

package org.example;

import java.util.Objects;

public class ProductA implements Product {
    private final Component sharedComponent;

    // تزریق وابستگی (Dependency Injection)
    public ProductA(Component sharedComponent) {
        // برنامه‌نویسی تدافعی برای جلوگیری از خطاهای احتمالی
        this.sharedComponent = Objects.requireNonNull(sharedComponent, "Component cannot be null");
    }

    @Override
    public void run() {
        sharedComponent.execute();
        System.out.println("ProductA logic done");
    }
}

src/main/java/org/example/ProductB.java
Java

package org.example;

import java.util.Objects;

public class ProductB implements Product {
    private final Component sharedComponent;

    public ProductB(Component sharedComponent) {
        this.sharedComponent = Objects.requireNonNull(sharedComponent, "Component cannot be null");
    }

    @Override
    public void run() {
        sharedComponent.execute();
        System.out.println("ProductB logic done");
    }
}


src/main/java/org/example/Main.java
Java

package org.example;

import java.util.List;

public class Main {
    public static void main(String[] args) {
      
        Component sharedComponent = new SharedComponent();

 
        Product productA = new ProductA(sharedComponent);
        Product productB = new ProductB(sharedComponent);

  
        List<Product> products = List.of(productA, productB);
        products.forEach(Product::run);
    }
}

---------------------------------------------------------------------------------------------------------------------------------------------
        localizationconsoleapp
        
        package com.example.localization;

import java.text.MessageFormat;
import java.util.Locale;
import java.util.ResourceBundle;

public class MessageProvider {
    private ResourceBundle bundle;

    public MessageProvider(String languageTag) {
        setLanguage(languageTag);
    }

    public void setLanguage(String languageTag) {
        // ⭐ ترفند سنیور: استفاده از forLanguageTag که استاندارد مدرن جاوا (BCP 47) است
        Locale locale = Locale.forLanguageTag(languageTag);
        this.bundle = ResourceBundle.getBundle("messages", locale);
    }

    public String get(String key) {
        return bundle.getString(key);
    }

    // ⭐ ترفند سنیور: استفاده از MessageFormat برای تزریق متغیرها به داخل رشته‌های ترجمه‌شده
    public String format(String key, Object... args) {
        return MessageFormat.format(bundle.getString(key), args);
    }
}
import java.util.Scanner;

public class LocalizationApp {
    public static void main(String[] args) {
        // استفاده از try-with-resources برای جلوگیری از نشت حافظه
        try (Scanner scanner = new Scanner(System.in)) {
            
            System.out.println("Select language / زبان را انتخاب کنید (en/fa): ");
            String initialLang = scanner.nextLine().trim().toLowerCase();
            initialLang = initialLang.equals("fa") ? "fa" : "en"; // Default to English
            
            // نمونه‌سازی از کلاسی که وضعیت زبان را مدیریت می‌کند
            MessageProvider messages = new MessageProvider(initialLang);
            System.out.println(messages.get("welcome"));

            // دریافت نام
            System.out.println(messages.get("enter.name"));
            String name = scanner.nextLine().trim();

            // پیشنهاد تغییر زبان (با پیام ترجمه‌شده بر اساس زبان فعلی)
            promptLanguageChange(scanner, messages);

            // دریافت سن
            System.out.println(messages.get("enter.age"));
            String age = scanner.nextLine().trim();

            // پیشنهاد مجدد تغییر زبان
            promptLanguageChange(scanner, messages);

            // ⭐ چاپ خلاصه با استفاده از MessageFormat که در تمام زبان‌ها ساختار درستی دارد
            System.out.println(messages.format("summary", name, age));
        }
    }

    private static void promptLanguageChange(Scanner scanner, MessageProvider messages) {
        // پیام تغییر زبان اکنون از ResourceBundle خوانده می‌شود، نه هاردکد!
        System.out.println(messages.get("change.language"));
        String input = scanner.nextLine().trim().toLowerCase();
        
        if (input.equals("en") || input.equals("fa")) {
            messages.setLanguage(input);
        }
    }
}-------------------extensibleprocessor
@FunctionalInterface
public interface StringProcessor {
    String process(String input);
}


Java

package com.example.processor;

public class UpperCaseProcessor implements StringProcessor {
    @Override
    public String process(String input) {
     
        return input == null ? null : input.toUpperCase();
    }
}


Java

package com.example.processor;

public class ExclamationProcessor implements StringProcessor {
    @Override
    public String process(String input) {
        return input == null ? null : input + "!";
    }
}


Java

package com.example.processor;

import java.util.List;
import java.util.Objects;
import java.util.stream.Collectors;

public class ProcessorManager {
    

    private final List<StringProcessor> processors;

    // تزریق وابستگی از طریق سازنده (Constructor Injection)
    public ProcessorManager(List<StringProcessor> processors) {
        // ایجاد یک کپی غیرقابل تغییر (Immutable) از لیست برای جلوگیری از دستکاری لیست از بیرون
        this.processors = List.copyOf(Objects.requireNonNull(processors, "Processors list cannot be null"));
    }

    public List<String> processAll(List<String> items) {
        if (items == null || items.isEmpty()) {
            return List.of();
        }

        return items.stream()
                .map(this::applyProcessors)
                .collect(Collectors.toList());
    }


    private String applyProcessors(String item) {
        String result = item;
        for (StringProcessor processor : processors) {
            result = processor.process(result);
        }
        return result;
    }
}


Java

package com.example.processor;

import java.util.List;

public class Main {
    public static void main(String[] args) {
        
  
        List<StringProcessor> pipeline = List.of(
                new UpperCaseProcessor(),
                new ExclamationProcessor()
                // new QuestionMarkProcessor() <- توسعه‌پذیری بی‌نهایت بدون تغییر سایر فایل‌ها
        );

        // ۲. تزریق خط لوله به منیجر
        ProcessorManager manager = new ProcessorManager(pipeline);

  
        List<String> data = List.of("Hello", "World", "Java");
        List<String> result = manager.processAll(data);

        result.forEach(System.out::println);
    }
}
-------------------------------------- javaauthconsuleapp
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
import java.util.Base64;
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

public class Main {
    
    record User(String username, String passwordHash, String salt) {}

  
    private static final Map<String, User> usersDatabase = new HashMap<>();

    public static void main(String[] args) {

        try (Scanner scanner = new Scanner(System.in)) {
            while (true) {
                System.out.println("\n--- Secure Auth System ---");
                System.out.println("1. Register\n2. Login\n3. Exit");
                System.out.print("Choose an option: ");
                
                String choice = scanner.nextLine().trim();

                switch (choice) {
                    case "1" -> registerUser(scanner);
                    case "2" -> loginUser(scanner);
                    case "3" -> {
                        System.out.println("Exiting System...");
                        return;
                    }
                    default -> System.out.println("Invalid option. Please try again.");
                }
            }
        }
    }

    private static void registerUser(Scanner scanner) {
        System.out.print("Enter username: ");
        String username = scanner.nextLine().trim();
        
        if (usersDatabase.containsKey(username)) {
            System.out.println("❌ Username already exists.");
            return;
        }

        System.out.print("Enter password: ");
        String password = scanner.nextLine();

        if (password.isBlank()) {
            System.out.println("❌ Password cannot be empty.");
            return;
        }

        // تولید نمک (Salt) کاملاً تصادفی برای این کاربر
        String salt = generateSalt();
        // هش کردن رمز عبور به همراه نمک
        String hashedPassword = hashPassword(password, salt);

        // ذخیره کاربر در سیستم
        usersDatabase.put(username, new User(username, hashedPassword, salt));
        System.out.println("✅ User registered successfully.");
    }

    private static void loginUser(Scanner scanner) {
        System.out.print("Enter username: ");
        String username = scanner.nextLine().trim();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();

        User user = usersDatabase.get(username);

        if (user != null) {
            // هش کردن رمز عبور وارد شده با نمکِ ذخیره شده در دیتابیس برای این کاربر
            String computedHash = hashPassword(password, user.salt());

            // ⭐ ترفند امنیتی سنیور: استفاده از MessageDigest.isEqual برای جلوگیری از Timing Attack
            if (MessageDigest.isEqual(user.passwordHash().getBytes(), computedHash.getBytes())) {
                System.out.println("✅ Login successful. Welcome, " + username + "!");
                return;
            }
        }
        
        // پیام خطای یکسان برای جلوگیری از لو رفتن وجود یا عدم وجود نام کاربری (Security Best Practice)
        System.out.println("❌ Invalid username or password.");
    }


    private static String generateSalt() {
        SecureRandom random = new SecureRandom();
        byte[] saltBytes = new byte[16];
        random.nextBytes(saltBytes);
        return Base64.getEncoder().encodeToString(saltBytes); // استفاده از Base64 به جای حلقه for و Hex
    }


    private static String hashPassword(String password, String saltBase64) {
        try {
            MessageDigest digest = MessageDigest.getInstance("SHA-256");
        
            digest.update(Base64.getDecoder().decode(saltBase64));
       
            byte[] hashBytes = digest.digest(password.getBytes(StandardCharsets.UTF_8));
            
            // بازگرداندن خروجی به صورت Base64 (بسیار تمیزتر و سریع‌تر از حلقه هگزادسیمال)
            return Base64.getEncoder().encodeToString(hashBytes);
            
        } catch (NoSuchAlgorithmException e) {
            throw new RuntimeException("Fatal Error: Hashing algorithm not found", e);
        }
    }
}
---------------------------------
javasystemcontinuitychallenge
package com.example.continuity;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.StandardOpenOption;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

public class DataStore {
    // مسیر فایل ذخیره‌سازی
    private static final Path FILE_PATH = Paths.get("database.txt");
    
    // ⭐ ترفند سنیور: استفاده از لیست Thread-Safe برای محیط‌های واقعی
    private final List<String> dataList;

    public DataStore() {
        this.dataList = new CopyOnWriteArrayList<>();
        recoverData(); // بازیابی خودکار داده‌ها در زمان بوت شدن سیستم
    }

    // متد خصوصی برای بازیابی داده‌ها (Continuity)
    private void recoverData() {
        try {
            if (Files.exists(FILE_PATH)) {
                // استفاده از NIO.2 برای خواندن بسیار سریع تمام خطوط
                dataList.addAll(Files.readAllLines(FILE_PATH));
            }
        } catch (IOException e) {
            System.err.println("Critical Error: Failed to recover data -> " + e.getMessage());
        }
    }

    public void addData(String data) {
        if (data == null || data.isBlank()) return; // برنامه‌نویسی تدافعی
        
        dataList.add(data);
        persistData(data); // بلافاصله در فایل ذخیره می‌شود
    }

    // متد خصوصی برای ذخیره دائم روی دیسک (Append Mode)
    private void persistData(String data) {
        try {
            // ⭐ ترفند سنیور: استفاده از Files.writeString با حالت APPEND و CREATE
            Files.writeString(FILE_PATH, data + System.lineSeparator(), 
                    StandardOpenOption.CREATE, 
                    StandardOpenOption.APPEND);
        } catch (IOException e) {
            System.err.println("Critical Error: Failed to persist data -> " + e.getMessage());
        }
    }

    public List<String> getAllData() {
        // ⭐ ترفند سنیور: بازگرداندن یک کپی غیرقابل تغییر (Immutable) برای جلوگیری از دستکاری از بیرون
        return List.copyOf(dataList);
    }
}

۲. کلاس Main.java (رابط کاربری تمیز و بدون منطق بیزینس)

حالا کلاس Main کاملاً خلوت شده است و هیچ اطلاعی از وجود فایل .txt ندارد!
Java

package com.example.continuity;

import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        // زمان نمونه‌سازی، DataStore خودش داده‌های قبلی را از فایل لود می‌کند
        DataStore dataStore = new DataStore();
        
        // مدیریت ایمن منابع با try-with-resources
        try (Scanner scanner = new Scanner(System.in)) {
            while (true) {
                System.out.println("\n=== Continuity System ===");
                System.out.println("1. Add Data");
                System.out.println("2. Show All Data");
                System.out.println("3. Exit");
                System.out.print("Choose an option: ");
                
                String input = scanner.nextLine().trim();

                // ⭐ استفاده از Switch Expression مدرن جاوا 21
                switch (input) {
                    case "1" -> {
                        System.out.print("Enter data: ");
                        String data = scanner.nextLine().trim();
                        dataStore.addData(data);
                        System.out.println("✅ Data saved securely.");
                    }
                    case "2" -> {
                        System.out.println("--- Database Records ---");
                        var allData = dataStore.getAllData();
                        if (allData.isEmpty()) {
                            System.out.println("No records found.");
                        } else {
                            allData.forEach(System.out::println);
                        }
                    }
                    case "3" -> {
                        System.out.println("Exiting System. Data is safe.");
                        return;
                    }
                    default -> System.out.println("❌ Invalid option. Try again.");
                }
            }
        }
    }
}
------------------------refactormaintainableprocessor


Operation.java:
Java

package org.example;

import java.util.List;

public interface Operation {
    String getName(); // نام عملیات برای نمایش در منو
    double execute(List<Integer> numbers); // منطق محاسبه
}


SumOperation.java:
Java

package org.example;
import java.util.List;

public class SumOperation implements Operation {
    @Override public String getName() { return "Sum"; }
    @Override public double execute(List<Integer> numbers) {
        return numbers.stream().mapToInt(Integer::intValue).sum();
    }
}

MultiplyOperation.java:
Java

package org.example;
import java.util.List;

public class MultiplyOperation implements Operation {
    @Override public String getName() { return "Multiply"; }
    @Override public double execute(List<Integer> numbers) {
        return numbers.stream().reduce(1, (a, b) -> a * b);
    }
}

MaxOperation.java:
Java

package org.example;
import java.util.List;

public class MaxOperation implements Operation {
    @Override public String getName() { return "Max"; }
    @Override public double execute(List<Integer> numbers) {
        return numbers.stream().max(Integer::compareTo).orElse(0);
    }
}

MinOperation.java:
Java

package org.example;
import java.util.List;

public class MinOperation implements Operation {
    @Override public String getName() { return "Min"; }
    @Override public double execute(List<Integer> numbers) {
        return numbers.stream().min(Integer::compareTo).orElse(0);
    }
}


Java

package org.example;
import java.util.List;

public class AverageOperation implements Operation {
    @Override public String getName() { return "Average"; }
    @Override public double execute(List<Integer> numbers) {
        return numbers.stream().mapToInt(Integer::intValue).average().orElse(0.0);
    }
}



Main.java:
Java

package org.example;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;
import java.util.ServiceLoader;

public class Main {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // ⭐ ترفند سنیور: کشف داینامیک کلاس‌ها با ServiceLoader (Java SPI)
        List<Operation> operations = new ArrayList<>();
        ServiceLoader.load(Operation.class).forEach(operations::add);

        if (operations.isEmpty()) {
            System.err.println("No operations found in the system!");
            return;
        }

        try (Scanner scanner = new Scanner(System.in)) {
            System.out.println("=== Dynamic Calculator ===");
            
            // تولید منو به صورت داینامیک از روی کلاس‌های موجود
            for (int i = 0; i < operations.size(); i++) {
                System.out.printf("%d) %s  ", (i + 1), operations.get(i).getName());
            }
            System.out.println("\nChoose operation:");

            if (scanner.hasNextInt()) {
                int opIndex = scanner.nextInt();
                
                // برنامه‌نویسی تدافعی: بررسی مرزهای ورودی کاربر
                if (opIndex > 0 && opIndex <= operations.size()) {
                    Operation selectedOp = operations.get(opIndex - 1);
                    double result = selectedOp.execute(numbers);
                    
                    // قالب‌بندی خروجی (اگر عدد صحیح است بدون اعشار چاپ شود)
                    if (result == (long) result) {
                        System.out.printf("Result of %s is: %d%n", selectedOp.getName(), (long) result);
                    } else {
                        System.out.printf("Result of %s is: %.2f%n", selectedOp.getName(), result);
                    }
                } else {
                    System.out.println("❌ Invalid operation selection.");
                }
            } else {
                System.out.println("❌ Invalid input. Please enter a number.");
            }
        }
    }
}
-------------------------------------
transactionprovacy
import javax.crypto.Cipher;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.GCMParameterSpec;
import javax.crypto.spec.PBEKeySpec;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.security.SecureRandom;
import java.security.spec.KeySpec;

public class TransactionManager {
    private static final Path FILE = Path.of("transaction.dat");
    
    // تنظیمات استاندارد رمزنگاری امن (Enterprise Grade)
    private static final String ENCRYPTION_ALGO = "AES/GCM/NoPadding";
    private static final int TAG_LENGTH_BIT = 128;
    private static final int IV_LENGTH_BYTE = 12; // 12 bytes is highly recommended for GCM
    private static final int SALT_LENGTH_BYTE = 16;
    private static final int ITERATION_COUNT = 65536; // جلوگیری از حملات Brute-Force
    private static final int KEY_LENGTH = 256;

    public void saveTransaction(String data) {
        if (data == null || data.isEmpty()) {
            throw new IllegalArgumentException("Transaction data cannot be empty");
        }

        try {
            // ۱. تولید Salt و IV کاملاً تصادفی برای این تراکنش
            byte[] salt = getRandomNonce(SALT_LENGTH_BYTE);
            byte[] iv = getRandomNonce(IV_LENGTH_BYTE);

            // ۲. ساخت کلید امن از روی رمز عبور
            SecretKey aesKeyFromPassword = getAESKeyFromPassword(getPassword(), salt);

            // ۳. پیکربندی Cipher برای رمزگذاری
            Cipher cipher = Cipher.getInstance(ENCRYPTION_ALGO);
            cipher.init(Cipher.ENCRYPT_MODE, aesKeyFromPassword, new GCMParameterSpec(TAG_LENGTH_BIT, iv));

            // ۴. رمزگذاری داده‌ها
            byte[] cipherText = cipher.doFinal(data.getBytes(StandardCharsets.UTF_8));

            // ۵. ترکیب (Concat) فایل خروجی: [Salt] + [IV] + [Encrypted Data]
            // این مقادیر (Salt و IV) راز نیستند و باید کنار داده ذخیره شوند تا در زمان رمزگشایی در دسترس باشند
            byte[] finalFileContent = ByteBuffer.allocate(salt.length + iv.length + cipherText.length)
                    .put(salt)
                    .put(iv)
                    .put(cipherText)
                    .array();

            Files.write(FILE, finalFileContent);
            
        } catch (Exception e) {
            throw new RuntimeException("Encryption failed! Secure data could not be saved.", e);
        }
    }

    public String loadTransaction() {
        if (!Files.exists(FILE)) {
            return "No transaction data found.";
        }

        try {
            byte[] fileContent = Files.readAllBytes(FILE);

            // ۱. بررسی طول فایل برای جلوگیری از خطای بافر
            if (fileContent.length < SALT_LENGTH_BYTE + IV_LENGTH_BYTE) {
                throw new IllegalArgumentException("Corrupted or invalid transaction file");
            }

            // ۲. استخراج Salt, IV و متن رمزنگاری شده از فایل
            ByteBuffer bb = ByteBuffer.wrap(fileContent);
            byte[] salt = new byte[SALT_LENGTH_BYTE];
            bb.get(salt);
            byte[] iv = new byte[IV_LENGTH_BYTE];
            bb.get(iv);
            byte[] cipherText = new byte[bb.remaining()];
            bb.get(cipherText);

            // ۳. بازتولید کلید از رمز عبور (باید با رمز عبور زمان ذخیره یکسان باشد)
            SecretKey aesKeyFromPassword = getAESKeyFromPassword(getPassword(), salt);

            // ۴. پیکربندی Cipher برای رمزگشایی
            Cipher cipher = Cipher.getInstance(ENCRYPTION_ALGO);
            cipher.init(Cipher.DECRYPT_MODE, aesKeyFromPassword, new GCMParameterSpec(TAG_LENGTH_BIT, iv));

            // ۵. رمزگشایی داده‌ها
            byte[] plainText = cipher.doFinal(cipherText);
            return new String(plainText, StandardCharsets.UTF_8);

        } catch (javax.crypto.AEADBadTagException e) {
            // خطای کلیدی GCM: رمز عبور اشتباه است یا فایل دستکاری شده است
            throw new SecurityException("Decryption failed! Incorrect password or corrupted data.", e);
        } catch (Exception e) {
            throw new RuntimeException("Decryption process encountered an error.", e);
        }
    }

    // متد کمکی: دریافت رمز عبور از محیط یا استفاده از مقدار پیش‌فرض
    private char[] getPassword() {
        String password = System.getenv("TX_PASSWORD");
        if (password == null || password.isEmpty()) {
            password = "DefaultSecurePassword123!"; // مقدار پیش‌فرض ایمن‌سازی شده
        }
        return password.toCharArray();
    }

    // تولید اعداد تصادفی امن (Cryptographically Secure)
    private byte[] getRandomNonce(int numBytes) {
        byte[] nonce = new byte[numBytes];
        new SecureRandom().nextBytes(nonce);
        return nonce;
    }

    // تبدیل رمز عبور انسانی به کلید 256 بیتی قدرتمند با استفاده از PBKDF2
    private SecretKey getAESKeyFromPassword(char[] password, byte[] salt) throws Exception {
        SecretKeyFactory factory = SecretKeyFactory.getInstance("PBKDF2WithHmacSHA256");
        KeySpec spec = new PBEKeySpec(password, salt, ITERATION_COUNT, KEY_LENGTH);
        SecretKey secret = new SecretKeySpec(factory.generateSecret(spec).getEncoded(), "AES");
        return secret;
    }
}
performancetunningaggreeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeegatttion
public class Main {
    public static void main(String[] args) {
        int dataSize = 200_000_000;
        
        // ⭐ ترفند سنیور ۱: استفاده از آرایه Primitive
        // مصرف رم از ۵ گیگابایت به حدود ۸۰۰ مگابایت کاهش می‌یابد و سربار Resize حذف می‌شود
        int[] data = new int[dataSize];
        Random random = new Random();
        
        for (int i = 0; i < dataSize; i++) {
            data[i] = random.nextInt(100);
        }

        long start = System.currentTimeMillis();
        
        // نتیجه باید long باشد تا سرریز نشود
        long sum = DataAggregator.aggregateSum(data); 
        
        long end = System.currentTimeMillis();
        System.out.println("Sum: " + sum);
        System.out.println("Elapsed: " + (end - start) + " ms");
    }
}

۲. فایل DataAggregator.java (پردازش موازی و ایمن)
Java

package com.example.performance;

import java.util.Arrays;

public class DataAggregator {
    
    // ⭐ ترفند سنیور ۲: دریافت آرایه primitive و بازگرداندن long
    public static long aggregateSum(int[] data) {
        
        // روش اول: استفاده از پردازش موازی (Parallel Stream)
        // این روش روی پردازنده‌های چند هسته‌ای برای ۲۰۰ میلیون رکورد فوق‌العاده سریع است
        return Arrays.stream(data)
                .parallel()
                .asLongStream() // تبدیل امن به Long برای جلوگیری از Overflow
                .sum();

        /* * روش دوم: حلقه for سنتی (CPU Cache Friendly)
         * اگر سیستم تک‌هسته‌ای باشد، این روش از استریم هم سریع‌تر است:
         * * long sum = 0;
         * for (int i = 0; i < data.length; i++) {
         * sum += data[i];
         * }
         * return sum;
         */
    }
}

-----
localizationconsuleaoo
prompt.language=Select language / انتخاب زبان (en/fa):
prompt.name=Enter your name:
greeting.hello=Hello {0}, welcome to our application!
error.invalid=Invalid input.

فایل messages_fa.properties (فایل فارسی):
Properties

prompt.name=لطفاً نام خود را وارد کنید:
greeting.hello=سلام {0}، به برنامه ما خوش آمدید!
error.invalid=ورودی نامعتبر است.

۲. کلاس I18nManager.java (مدیریت متمرکز زبان - Abstraction)

این کلاس وظیفه کپسوله‌سازی منطق ترجمه را بر عهده دارد تا کد اصلی شلوغ نشود.
Java

package com.example.localization;

import java.text.MessageFormat;
import java.util.Locale;
import java.util.ResourceBundle;

public class I18nManager {
    private ResourceBundle bundle;

    // بارگذاری پیش‌فرض با زبان انگلیسی
    public I18nManager() {
        setLocale(Locale.ENGLISH);
    }

    public void setLocale(Locale locale) {
        this.bundle = ResourceBundle.getBundle("messages", locale);
    }

    // متد منعطف برای گرفتن پیام (با یا بدون پارامتر)
    public String getMessage(String key, Object... args) {
        if (bundle == null || !bundle.containsKey(key)) {
            return "!" + key + "!"; // مدیریت خطای نبودن کلید در فایل
        }
        String message = bundle.getString(key);
        return MessageFormat.format(message, args);
    }
}

۳. کلاس LocalizationApp.java (نقطه ورود تمیز و بدون هاردکد)
Java

package com.example.localization;

import java.util.Locale;
import java.util.Scanner;

public class LocalizationApp {
    public static void main(String[] args) {
        // مدیریت ایمن منابع با try-with-resources
        try (Scanner scanner = new Scanner(System.in)) {
            
            // نمونه‌سازی مدیر زبان (پیش‌فرض انگلیسی برای پرسیدن سوال اول)
            I18nManager i18n = new I18nManager();

            // هیچ متنی هاردکد نشده است! پیام انتخاب زبان از فایل خوانده می‌شود
            System.out.println(i18n.getMessage("prompt.language"));
            String lang = scanner.nextLine().trim().toLowerCase();

            // ⭐ ترفند سنیور: استفاده از Locale.of به جای سازنده منسوخ‌شده
            // همچنین استفاده از برنامه‌نویسی تدافعی برای مقادیر نامعتبر
            Locale selectedLocale = lang.equals("fa") ? Locale.of("fa") : Locale.of("en");
            
            // تغییر زبان برنامه بر اساس انتخاب کاربر
            i18n.setLocale(selectedLocale);

            // دریافت نام با پیام ترجمه‌شده
            System.out.println(i18n.getMessage("prompt.name"));
            String name = scanner.nextLine().trim();

            if (name.isEmpty()) {
                System.out.println(i18n.getMessage("error.invalid"));
                return;
            }

            // چاپ پیام نهایی داینامیک
            System.out.println(i18n.getMessage("greeting.hello", name));
        }
    }
}
-----------------------
-----------------------
intallabilityevaluator
public class InstallabilityEvaluator {

    public static void main(String[] args) {
        List<ChallengeReport> reports = new ArrayList<>();

        // ۱. مدیریت ایمن منابع (try-with-resources) برای جلوگیری از Memory Leak
        try (Scanner scanner = new Scanner(System.in)) {
            
            // برنامه‌نویسی تدافعی: بررسی صحت ورودی عدد
            if (!scanner.hasNextLine()) return;
            int n;
            try {
                n = Integer.parseInt(scanner.nextLine().trim());
            } catch (NumberFormatException e) {
                System.err.println("Error: Invalid number format for platform count.");
                return;
            }

            for (int i = 0; i < n; i++) {
                if (!scanner.hasNextLine()) break;
                
                String platform = scanner.nextLine().trim();
                String timeRequired = scanner.nextLine().trim();
                String manualEffort = scanner.nextLine().trim();
                String automationLevel = scanner.nextLine().trim();

                reports.add(new ChallengeReport(platform, timeRequired, manualEffort, automationLevel));
            }

            // ⭐ ترفند سنیور: استفاده از Collectors.joining
            // این متد به صورت کاملاً خودکار و بهینه، مشکل کامای آخر و براکت‌های ابتدا و انتها را حل می‌کند
            String jsonOutput = reports.stream()
                    .map(ChallengeReport::toJson)
                    .collect(Collectors.joining(",\n", "[\n", "\n]"));

            System.out.println(jsonOutput);

        } catch (Exception e) {
            System.err.println("An unexpected error occurred: " + e.getMessage());
        }
    }

    // ⭐ ترفند سنیور: استفاده از Record جاوا برای دیتای تغییرناپذیر (Immutable)
    record ChallengeReport(String platform, String timeRequired, String manualEffort, String automationLevel) {
        
        public String toJson() {
            // استفاده از Text Blocks (جاوا 15+) و متد formatted برای خوانایی بی‌نظیر
            return """
                    {"platform":"%s","timeRequired":"%s","manualEffort":"%s","automationLevel":"%s"}"""
                    .formatted(
                            escapeJson(platform),
                            escapeJson(timeRequired),
                            escapeJson(manualEffort),
                            escapeJson(automationLevel)
                    );
        }

        // برنامه‌نویسی تدافعی: جلوگیری از خراب شدن ساختار JSON در صورت ورود کاراکتر (") توسط کاربر
        private String escapeJson(String input) {
            if (input == null) return "";
            return input.replace("\"", "\\\"");
        }
    }
}
--------------------------
flexibleprocessorframework

        package com.example.processor;

public interface Processor {
    // نامی که کلاینت برای صدا زدن این پردازشگر استفاده می‌کند
    String getName(); 
    
    // منطق پردازش
    String process(String input);
}

۲. پیاده‌سازی پردازشگرها (مستقل و کپسوله)

فایل UppercaseProcessor.java:
Java

package com.example.processor;

public class UppercaseProcessor implements Processor {
    @Override
    public String getName() {
        return "uppercase";
    }

    @Override
    public String process(String input) {
        return input == null ? null : input.toUpperCase(); // برنامه‌نویسی تدافعی
    }
}

فایل ReverseProcessor.java:
Java

package com.example.processor;

public class ReverseProcessor implements Processor {
    @Override
    public String getName() {
        return "reverse";
    }

    @Override
    public String process(String input) {
        return input == null ? null : new StringBuilder(input).reverse().toString();
    }
}



package com.example.processor;

import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class ProcessorManager {
    
    // ⭐ ترفند سنیور: استفاده از ConcurrentHashMap برای اطمینان از Thread-Safety در سیستم‌های همزمان
    private final Map<String, Processor> registry = new ConcurrentHashMap<>();

    // متد ثبت‌نام (Register) برای اضافه کردن داینامیک پردازشگرها
    public void register(Processor processor) {
        if (processor != null) {
            registry.put(processor.getName().toLowerCase(), processor);
        }
    }

    // متد پردازش با استفاده از نامِ پردازشگر (بدون وابستگی به کلاس واقعی)
    public String process(String processorName, String input) {
        Processor processor = registry.get(processorName.toLowerCase());
        
        if (processor == null) {
            throw new IllegalArgumentException("Processor not found: " + processorName);
        }
        
        return processor.process(input);
    }
}


Java

package com.example.processor;

public class Main {
    public static void main(String[] args) {
        ProcessorManager manager = new ProcessorManager();
        
        // ۱. مرحله پیکربندی سیستم (Bootstrapping): پردازشگرها رجیستر می‌شوند
        manager.register(new UppercaseProcessor());
        manager.register(new ReverseProcessor());
        // manager.register(new ReplaceSpaceProcessor()); <-- افزودن افزونه جدید به همین سادگی است

        String input = "hello world";

        // ۲. مرحله استفاده (Execution): کلاینت فقط نام استراتژی را صدا می‌زند، نه کلاس آن را!
        try {
            System.out.println("Uppercase: " + manager.process("uppercase", input));
            System.out.println("Reverse: " + manager.process("reverse", input));
            
            // تست برنامه‌نویسی تدافعی
            System.out.println(manager.process("unknown", input)); 
        } catch (IllegalArgumentException e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}
--------------------------------crossplatformdbcconnector
package org.example;

import java.sql.SQLException;

public interface DatabaseConnector extends AutoCloseable {
    void connect(String url, String username, String password) throws SQLException;
    void executeQuery(String query);
    void disconnect();

    // ⭐ ترفند سنیور: پیاده‌سازی پیش‌فرض برای AutoCloseable تا try-with-resources به درستی کار کند
    @Override
    default void close() throws Exception {
        disconnect();
    }
}

۲. کلاس DatabaseConnectorFactory.java (قلب پورتابیلیتی)

این کلاس وظیفه دارد بر اساس ورودی، شیء مناسب را بسازد تا کلاس Main هیچ‌وقت درگیر new کردن کلاس‌های خاص نشود.
Java

package org.example;

public class DatabaseConnectorFactory {
    
    // جلوگیری از نمونه‌سازی (Instantiation) کلاس فکتوری
    private DatabaseConnectorFactory() {}

    public static DatabaseConnector getConnector(String dbType) {
        // برنامه‌نویسی تدافعی: مدیریت مقادیر خالی یا Null
        if (dbType == null || dbType.isBlank()) {
            throw new IllegalArgumentException("Database type is missing! Please use -DdbType=oracle or -DdbType=sap");
        }

        // ⭐ ترفند سنیور: استفاده از Switch Expression مدرن جاوا (بسیار تمیزتر از if-else)
        return switch (dbType.trim().toLowerCase()) {
            case "oracle" -> new OracleDatabaseConnector();
            case "sap" -> new SapDatabaseConnector();
            default -> throw new IllegalArgumentException("Unsupported database type: " + dbType);
        };
    }
}

۳. پیاده‌سازی کانکتورها (مستقل و ایزوله)

فایل OracleDatabaseConnector.java:
Java

package org.example;

import java.sql.SQLException;

public class OracleDatabaseConnector implements DatabaseConnector {
    @Override
    public void connect(String url, String username, String password) throws SQLException {
        System.out.println("Connecting to Oracle DB securely at " + url + "...");
    }

    @Override
    public void executeQuery(String query) {
        System.out.println("Executing query on Oracle DB: " + query);
    }

    @Override
    public void disconnect() {
        System.out.println("Disconnecting from Oracle DB...");
    }
}

فایل SapDatabaseConnector.java:
Java

package org.example;

import java.sql.SQLException;

public class SapDatabaseConnector implements DatabaseConnector {
    @Override
    public void connect(String url, String username, String password) throws SQLException {
        System.out.println("Connecting to SAP DB securely at " + url + "...");
    }

    @Override
    public void executeQuery(String query) {
        System.out.println("Executing query on SAP DB: " + query);
    }

    @Override
    public void disconnect() {
        System.out.println("Disconnecting from SAP DB...");
    }
}

۴. کلاس Main.java (نقطه ورود تمیز و داینامیک)

اکنون کلاس اصلی هیچ اطلاعی از Oracle یا SAP ندارد و همه چیز را به Factory می‌سپارد.
Java

package org.example;

public class Main {
    public static void main(String[] args) {
        // دریافت نوع دیتابیس از System Properties (همان -DdbType در دستور اجرای Maven)
        String dbType = System.getProperty("dbType");

        // پارامترهای فرضی دیتابیس
        String url = "jdbc:dummy://localhost:1521/mydb";
        String username = "admin";
        String password = "secure_password";

        try {
            // ⭐ ترفند سنیور: استفاده از try-with-resources. 
            // با این کار حتی اگر کوئری Exception بدهد، متد disconnect (از طریق close) حتماً صدا زده می‌شود!
            try (DatabaseConnector connector = DatabaseConnectorFactory.getConnector(dbType)) {
                
                connector.connect(url, username, password);
                connector.executeQuery("SELECT * FROM USERS");
                
            }
        } catch (IllegalArgumentException e) {
            // مدیریت خطای تنظیمات (مثلاً زمانی که -DdbType ارسال نشده باشد)
            System.err.println("Configuration Error: " + e.getMessage());
        } catch (Exception e) {
            // مدیریت خطاهای دیتابیس
            System.err.println("Database Execution Error: " + e.getMessage());
        }
    }
}


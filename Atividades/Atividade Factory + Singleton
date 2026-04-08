// Código feito em aula por Artur Figueiredo, Enrique Campos Nogueira, João Victor dos Santos Silva


class GlobalConfig {
    private static GlobalConfig instance;
    
    private String nomeApp = "Sistema de Notificações";
    private String servidor = "servidor.empresa.com";
    private int maxRetries = 3;

    private GlobalConfig() {}

    public static GlobalConfig getInstance() {
        if (instance == null) {
            instance = new GlobalConfig();
        }
        return instance;
    }

    public String getNomeApp() { return nomeApp; }
    public String getServidor() { return servidor; }
    public int getMaxRetries() { return maxRetries; }
}

interface Notification {
    void send(String msg);
}

class EmailNotification implements Notification {
    public void send(String msg) {
        System.out.println("Enviando E-mail: " + msg);
    }
}

class SMSNotification implements Notification {
    public void send(String msg) {
        System.out.println("Enviando SMS: " + msg);
    }
}

class PushNotification implements Notification {
    public void send(String msg) {
        System.out.println("Enviando Push Notification: " + msg);
    }
}

class NotificationFactory {
    public static Notification create(String type) {
        if (type == null) return null;
        
        switch (type.toUpperCase()) {
            case "EMAIL": return new EmailNotification();
            case "SMS":   return new SMSNotification();
            case "PUSH":  return new PushNotification();
            default: throw new IllegalArgumentException("Tipo de notificação desconhecido.");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        
        GlobalConfig config = GlobalConfig.getInstance();
        System.out.println("Iniciando " + config.getNomeApp() + " no servidor " + config.getServidor());
        
        Notification email = NotificationFactory.create("EMAIL");
        Notification sms = NotificationFactory.create("SMS");
        Notification push = NotificationFactory.create("PUSH");

        email.send("Mensagem por e-mail");
        sms.send("Mensagem por sms");
        push.send("mensagem por push");
        
        // Chamada da suíte de testes adicionada ao final da execução principal
        System.out.println("\n=================================");
        SimpleTests.main(args);
    }
}

// 4. Testes Manuais (Sem frameworks externos)
class SimpleTests {
    public static void main(String[] args) {
        System.out.println("--- Iniciando Testes ---");
        testSingletonGaranteInstanciaUnica();
        testFactoryCriaObjetosCorretos();
        testFactoryLancaExcecaoTipoInvalido();
        System.out.println("--- Todos os testes finalizados ---");
    }

    public static void testSingletonGaranteInstanciaUnica() {
        GlobalConfig config1 = GlobalConfig.getInstance();
        GlobalConfig config2 = GlobalConfig.getInstance();
        
        // Verifica se ambas as variáveis apontam para o mesmo endereço de memória
        if (config1 == config2) {
            System.out.println("[OK] testSingletonGaranteInstanciaUnica");
        } else {
            System.out.println("[FALHA] testSingleton: Instâncias diferentes foram criadas!");
        }
    }

    public static void testFactoryCriaObjetosCorretos() {
        Notification email = NotificationFactory.create("EMAIL");
        Notification sms = NotificationFactory.create("SMS");
        Notification push = NotificationFactory.create("PUSH");
        
        // Valida se as instâncias retornadas são do tipo correto
        boolean sucesso = (email instanceof EmailNotification) && 
                          (sms instanceof SMSNotification) && 
                          (push instanceof PushNotification);
                          
        if (sucesso) {
            System.out.println("[OK] testFactoryCriaObjetosCorretos");
        } else {
            System.out.println("[FALHA] testFactory: Tipos incorretos retornados!");
        }
    }
    
    public static void testFactoryLancaExcecaoTipoInvalido() {
        try {
            NotificationFactory.create("FAX");
            // Se chegar aqui, a exceção não foi lançada
            System.out.println("[FALHA] testFactoryExcecao: Nenhuma exceção foi lançada para tipo inválido!");
        } catch (IllegalArgumentException e) {
            // Comportamento esperado
            System.out.println("[OK] testFactoryLancaExcecaoTipoInvalido");
        }
    }
}

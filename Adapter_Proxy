// Padrão Singleton
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

// Interface de notificação 
interface Notification {
    void send(String target, String msg);
}

// Classes de notificação por e-mail e push
class EmailNotification implements Notification {
    public void send(String target, String msg) {
        System.out.println("Enviando E-mail para [" + target + "]: " + msg);
    }
}
class PushNotification implements Notification {
    public void send(String target, String msg) {
        System.out.println("Enviando Push Notification para [" + target + "]: " + msg);
    }
}

// API de SMS (serviço legado incompatível com send())
class LegacySMSApi {
    public void dispatchSMS(String targetNumber, String content) {
        System.out.println("API Externa (Legada) despachando SMS para [" + targetNumber + "]: " + content);
    }
}

// Padrão Adapter
class SMSAdapter implements Notification {
    private LegacySMSApi legacyApi;

    public SMSAdapter(LegacySMSApi legacyApi) {
        this.legacyApi = legacyApi;
    }

    @Override
    public void send(String target, String msg) {
        legacyApi.dispatchSMS(target, msg);
    }
}

// Padrão Proxy
class NotificationProxy implements Notification {
    private Notification realNotification;
    private boolean userHasPermission;
    private int attempts;

    public NotificationProxy(Notification realNotification, boolean userHasPermission) {
        this.realNotification = realNotification;
        this.userHasPermission = userHasPermission;
        this.attempts = 0;
    }

    @Override
    public void send(String target, String msg) {
        System.out.println("\n[PROXY LOG] Interceptando requisição de envio para: " + target);

        if (!userHasPermission) {
            System.out.println("[PROXY AVISO] Falha na validação: Usuário sem permissão para enviar notificações.");
            return;
        }

        int maxRetries = GlobalConfig.getInstance().getMaxRetries();
        if (attempts >= maxRetries) {
            System.out.println("[PROXY AVISO] Limite de tentativas excedido (" + maxRetries + "). Bloqueando envio.");
            return;
        }

        attempts++;
        
        realNotification.send(target, msg);
        System.out.println("[PROXY LOG] Sucesso! Tentativa " + attempts + " de " + maxRetries + " utilizadas.");
    }
}

class NotificationFactory {
    public static Notification create(String type) {
        if (type == null) return null;
        
        switch (type.toUpperCase()) {
            case "EMAIL": 
                return new EmailNotification();
            case "SMS":   
                return new SMSAdapter(new LegacySMSApi());
            case "PUSH":  
                return new PushNotification();
            default: 
                throw new IllegalArgumentException("Tipo de notificação desconhecido.");
        }
    }
}

// --- Classe Main ---
public class Main {
    public static void main(String[] args) {
        
        GlobalConfig config = GlobalConfig.getInstance();
        System.out.println("Iniciando " + config.getNomeApp() + " no servidor " + config.getServidor());
        System.out.println("--------------------------------------------------");
        
        Notification baseSms = NotificationFactory.create("SMS");
        Notification baseEmail = NotificationFactory.create("EMAIL");

        Notification proxySms = new NotificationProxy(baseSms, true);
        Notification proxyEmailForbidden = new NotificationProxy(baseEmail, false);
        Notification proxyEmailAllowed = new NotificationProxy(baseEmail, true);
        
        // Vai gerar erro porque não tem permissão
        proxyEmailForbidden.send("cliente@empresa.com", "Bem-vindo ao sistema por E-mail!");
        
        // Não vai gerar erro porque tem permissão
        proxyEmailAllowed.send("cliente@empresa.com", "Bem-vindo ao sistema por E-mail!");
        
        // Três tentativas permitidas
        proxySms.send("11999998888", "Seu código de verificação é 1234.");
        proxySms.send("11999998888", "Promoção imperdível hoje!");
        proxySms.send("11999998888", "Sua fatura fechou.");
        
        // ERRO: MaxRetries < attempts
        proxySms.send("11999998888", "Mensagem extra que deve ser bloqueada.");
    }
}

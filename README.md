# poa

// Classe única para todos os tipos de conta (POO)
public class Conta {
    private String numeroConta;
    private double saldo;
    private TipoConta tipo; // Enum para os tipos de conta


    public enum TipoConta {
        CORRENTE, SALARIO, POUPANCA, INVESTIMENTO
    }


    public Conta(String numeroConta, double saldoInicial, TipoConta tipo) {
        this.numeroConta = numeroConta;
        this.saldo = saldoInicial;
        this.tipo = tipo;
    }


    public void sacar(double valor) {
        // Lógica básica de saque (o aspecto irá interceptar antes)
        saldo -= valor;
    }


    public void depositar(double valor) {
        saldo += valor;
    }


    public double getSaldo() {
        return saldo;
    }


    public TipoConta getTipo() {
        return tipo;
    }
}


// Aspecto para verificação de saldo (POA)
public aspect VerificacaoSaldoAspect {
    pointcut operacaoSaque(): execution(* Conta.sac*(double));


    before(double valor): operacaoSaque() && args(valor) {
        Conta conta = (Conta)thisJoinPoint.getTarget();
        if (conta.getSaldo() < valor) {
            String msg = String.format(
                "Saldo insuficiente na conta %s (Tipo: %s). Saldo: %.2f, Valor solicitado: %.2f",
                conta.getNumeroConta(),
                conta.getTipo(),
                conta.getSaldo(),
                valor
            );
            Logger.logErro(msg);
            throw new SaldoInsuficienteException(msg);
        }
    }
}


// Classe de exceção personalizada
public class SaldoInsuficienteException extends RuntimeException {
    public SaldoInsuficienteException(String message) {
        super(message);
    }
}


// Classe utilitária para logging
public class Logger {
    public static void logErro(String mensagem) {
        System.err.println("[ERRO] " + new java.util.Date() + " - " + mensagem);
    }
}

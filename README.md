# Criando-Transa-es-Executando-Backup-e-Recovery-de-Banco-de-Dados
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class TransacaoExemplo {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/seu_banco_de_dados";
        String user = "seu_usuario";
        String password = "sua_senha";

        Connection connection = null;
        Statement statement = null;

        try {
            connection = DriverManager.getConnection(url, user, password);
            connection.setAutoCommit(false); // Desabilita o auto-commit

            statement = connection.createStatement();
            statement.executeUpdate("INSERT INTO tabela_exemplo (coluna1) VALUES ('valor1')");
            statement.executeUpdate("INSERT INTO tabela_exemplo (coluna1) VALUES ('valor2')");

            connection.commit(); // Confirma as alterações
            System.out.println("Transação realizada com sucesso!");

        } catch (SQLException e) {
            if (connection != null) {
                try {
                    connection.rollback(); // Reverte as alterações em caso de erro
                    System.out.println("Transação revertida!");
                } catch (SQLException rollbackEx) {
                    rollbackEx.printStackTrace();
                }
            }
            e.printStackTrace();
        } finally {
            try {
                if (statement != null) statement.close();
                if (connection != null) connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

import java.io.IOException;

public class BackupExemplo {
    public static void main(String[] args) {
        String databaseName = "seu_banco_de_dados";
        String user = "seu_usuario";
        String password = "sua_senha";
        String backupFile = "backup.sql";

        String command = String.format("mysqldump -u %s -p%s %s > %s", user, password, databaseName, backupFile);

        try {
            Process process = Runtime.getRuntime().exec(command);
            process.waitFor();
            System.out.println("Backup realizado com sucesso!");
        } catch (IOException | InterruptedException e) {
            e.printStackTrace();
        }
    }
}

import java.io.IOException;

public class RecoveryExemplo {
    public static void main(String[] args) {
        String databaseName = "seu_banco_de_dados";
        String user = "seu_usuario";
        String password = "sua_senha";
        String backupFile = "backup.sql";

        String command = String.format("mysql -u %s -p%s %s < %s", user, password, databaseName, backupFile);

        try {
            Process process = Runtime.getRuntime().exec(command);
            process.waitFor();
            System.out.println("Banco de dados recuperado com sucesso!");
        } catch (IOException | InterruptedException e) {
            e.printStackTrace();
        }
    }
}

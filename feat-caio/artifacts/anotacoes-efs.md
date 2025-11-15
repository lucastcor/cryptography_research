# EFS - Notas

Ordem para proteger o arquivo:
1. FEK (chave simétrica) é criada aleatoriamente.
2. Conteúdo é criptografado com AES usando a FEK.
3. FEK é criptografada com RSA (chave pública do usuário).
4. FEK criptografada fica armazenada junto do arquivo.
5. Apenas quem possui a chave privada RSA consegue abrir o arquivo.

NTFS:
- Sistema de arquivos padrão do Windows.

X.509:
- Padrão de certificados digitais usados para armazenar a chave pública usada pelo EFS.

# Anotações Técnicas – EFS e APIs do Windows

## EncryptFileA  
- Função para criptografar um arquivo ou diretório: `BOOL EncryptFileA(LPCSTR lpFileName)`  
- Requer **acesso exclusivo** ao arquivo; se outro processo estiver usando o arquivo, a chamada falha.
- Se o arquivo já estiver criptografado, a função devolve sucesso sem fazer nada novo (numero que não e zero).
- Se o arquivo estiver compactado, ele é **descompactado antes** de ser criptografado. 

## DecryptFileA  
- Usada para descriptografar arquivos.  
- Se o arquivo **não estiver criptografado**, a função retorna sucesso (numero que não e zero), mas não altera o arquivo.

## CreateFile + Criptografia  
- Para criptografar um novo arquivo pela API, é necessário usar `CreateFile` com a flag `FILE_ATTRIBUTE_ENCRYPTED`.
- Isso marca o arquivo para ser criptografado pelo sistema de arquivos NTFS.

## Backup de arquivos criptografados  
- A API `OpenEncryptedFileRawA` permite abrir um arquivo criptografado no estado “bruto” (criptografado) para leitura ou escrita sem descriptografá-lo.
- Esse mecanismo é usado para backup: programas com privilégios podem ler os dados criptografados diretamente, sem ter a chave privada.

---
# Extra
Esses detalhes são úteis para entender os limites práticos do EFS: por exemplo, se um arquivo estiver aberto por outro programa, você **não poderá criptografá-lo naquele momento** via API. Também mostra que há suporte para backup seguro sem expor os dados criptografados.


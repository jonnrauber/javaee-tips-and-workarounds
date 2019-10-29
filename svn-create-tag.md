# Forma correta de criar tag no svn

`svn copy` permite ao _svn_ manter o histórico de atualizações dos arquivos movimentados, além de armazená-los de maneira mais eficiente.

```sh
svn copy http://svn.example.com/project/trunk \
      http://svn.example.com/project/tags/1.0 -m "Release 1.0"
```
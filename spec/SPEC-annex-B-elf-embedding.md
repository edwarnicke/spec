# Annex B

Annex B contains a method of embedding OmniBOR ID into ELF files.

## Embedded Artifact Dependency Graph Identifier
If an artifact contains an embedded artifact dependency graph identifier, then implementations MUST conform to the file format recommendations specified in this document.
If an artifact does not contain an embedded artifact dependency graph identifier, then implementations SHOULD look for that information in alternate locations. Tools may store and retrieve such information in alternate formats.

### Gitoid persistence by a Build Tool in ELF Objects/Executables
When persisting the gitoid to an ELF object or an ELF executable, the build tool should create a [section](https://refspecs.linuxfoundation.org/LSB_3.0.0/LSB-PDA/LSB-PDA.junk/sections.html) ```.note.omnibor``` and place the gitoid in the descriptor field of the Note entry. This section must be of type SHT_NOTE and must have the attribute SHF_ALLOC. Multiple Note entries must be created, one for each gitoid type when multiple gitoid types are involved. Each Note entry must contain the following fields in the same order as given below:
1. namesz (4 bytes): This field must be set to a value of 8, the length of the 'owner' field ```OMNIBOR\0``` in bytes.
2. descz (4 bytes): This field must contain the length of the gitoid in bytes.
3. type (4 bytes): This field must contain the value associated with one of the reserved gitoid types or a custom artifact identifier type.
   The values for the reserved types are in the range of 0x00000000 to 0x7fffffff. Permissible gitoid types with reserved values are:
   ```
     NT_GITOID_BLOB_SHA1 = 0x1,
     NT_GITOID_BLOB_SHA256 = 0x2,
   ```
   Custom artifact identifier types must use a value in the range of 0x80000000 to 0xffffffff.
4. owner (8 bytes): This field must contain the string "OMNIBOR\0", padded to 8 bytes.
5. descriptor: This field must contain the gitoids as raw bytes. The length of this field is the same as the value in 'descz' field.

When recording multiple gitoids in the Note section,
1. There must be only one Note entry for each gitoid type.
2. The Note entries must be in ascending order of gitoid type.

```
  Conforming build tool must generate both sha1 and sh256 gitoids.
```
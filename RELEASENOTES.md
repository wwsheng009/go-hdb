# Release Notes

## v1.2.0

### Minor revisions

#### v1.2.1

- updated dependencies

This version is mainly about performance improvements and source code cleanups.

### Incompatible changes

- Package driver/sqltrace was removed. For enabling / disabling connection SQL trace please use driver method driver.SetSQLTrace instead.
- Flag hdb.protocol.trace was renamed to hdb.protTrace.

Most go-hdb users shouldn't be affected by these incompatible changes.

## v1.1.0

### Minor revisions

#### v1.1.7

- replaced build tag 'edan' by connector attribute EmptyDateAsNull

#### v1.1.6

- fixed https://github.com/SAP/go-hdb/issues/113

#### v1.1.5

- added build tag 'edan' to return NULL in case of empty dates for all data format versions
- removed old build tags from source code

#### v1.1.4

- performance improvements
- updated dependencies

#### v1.1.3

- updated dependencies

#### v1.1.2

- updated dependencies

#### v1.1.1

- fixed slice resize runtime error: slice bounds out of range

This version is mainly about performance improvements and source code cleanups.

### Changes

Formerly with a ping interval defined the driver pinged all open connections periodically. Now a ping
is executed only when an idle connection is reused and the time since the last connection access
is greater than the ping interval. This avoids keeping idle connections to the server alive, improves
performance and is a compatible change from a driver's usage perspective.

## v1.0.0

### Minor revisions

#### v1.0.9

- upgraded dependencies

#### v1.0.8

- test performance improvements

#### v1.0.7

- added implicit instantiation of NullLob and NullDecimal owned references in respective Scan methods

#### v1.0.6

- some minor performance improvements and additional test

#### v1.0.5

- fixed blob bulk sql statement issue

#### v1.0.4

- fixed panic in case of bulk sql statement execution error

#### v1.0.3

- fixed scan type for 'nullable' database fields

#### v1.0.2

- fixed go.mod Go version

#### v1.0.1

- added Go 1.20 Unwrap() to driver.Error()

### Incompatible changes

Removal of already deprecated driver.NullTime alias (please use sql.NullTime instead).

Bulk operations:

- The following bulk operations are no longer supported:
  - via query ("bulk insert ...")
  - via named arguments (Flush / NoFlush)
  - via 'many' supporting one and two dimensional slices, arrays
- Please use the following bulk operations instead:
  - via extended parameter list with (len(args)%'#of paramerters' == 0
  - via function argument (func(args []any) error)

Stored procedures:

- Calling stored procedures with sql.Query methods are no longer supported.
- Please use sql.Exec methods instead and [sql.Rows](https://golang.org/pkg/database/sql/#Rows) for table output parameters.

### New features:

- Stored procedures executed by sql.Exec with parameters do
  - support [named](https://pkg.go.dev/database/sql#Named) parameters and
  - support [out](https://pkg.go.dev/database/sql#Out) output parameters
- Custom types for reading Lobs

  Whereas string and []byte types are supported as Lob input parameters for output parameters and query results (scan) the driver.Lob type was needed.
  With the help of one of the following functions a string, []byte or io.Writer based custom type can now be used as well:

  - driver.ScanLobBytes
  - driver.ScanLobString
  - driver.ScanLobWriter

  Example:

  ```golang
  // BytesLob defines a []byte based data type for scanning Lobs.
  type BytesLob []byte
  // Scan implements the database.sql.Scanner interface.
  func (b *BytesLob) Scan(arg any) error { return driver.ScanLobBytes(arg, (*[]byte)(b)) }
  ```

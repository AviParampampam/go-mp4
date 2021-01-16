go-mp4
------

[![Go Reference](https://pkg.go.dev/badge/github.com/abema/go-mp4.svg)](https://pkg.go.dev/github.com/abema/go-mp4)
[![CircleCI](https://circleci.com/gh/abema/go-mp4.svg?style=svg)](https://circleci.com/gh/abema/go-mp4)
[![codecov](https://codecov.io/gh/abema/go-mp4/branch/master/graph/badge.svg)](https://codecov.io/gh/abema/go-mp4)
[![Go Report Card](https://goreportcard.com/badge/github.com/abema/go-mp4)](https://goreportcard.com/report/github.com/abema/go-mp4)

go-mp4 is Go library for reading and writing MP4.

## Integration with your Go application

You can parse MP4 file as follows:

```go
// expand all boxes
_, err := mp4.ReadBoxStructure(file, func(h *mp4.ReadHandle) (interface{}, error) {
	fmt.Println("depth", len(h.Path))

	// Box Type (e.g. "mdhd", "tfdt", "mdat")
	fmt.Println("type", h.BoxInfo.Type.String())

	// Box Size
	fmt.Println("size", h.BoxInfo.Size)

	if h.BoxInfo.IsSupportedType() {
		// Payload
		box, _, err := h.ReadPayload()
		if err != nil {
			return nil, err
		}
		str, err := mp4.Stringify(box, h.BoxInfo.Context)
		if err != nil {
			return nil, err
		}
		fmt.Println("payload", str)

		// Expands children
		return h.Expand()
	}
	return nil, nil
})
```

```go
// extract specific boxes
boxes, err := mp4.ExtractBox(file, nil, mp4.BoxPath{mp4.BoxTypeMoov(), mp4.BoxTypeTrak(), mp4.BoxTypeTkhd()})
```

You can create additional box definition as follows:

```go
func BoxTypeXxxx() BoxType { return StrToBoxType("xxxx") }

func init() {
	AddBoxDef(&Xxxx{}, 0)
}

type Xxxx struct {
	FullBox  `mp4:"0,extend"`
	UI32      uint32 `mp4:"1,size=32"`
	ByteArray []byte `mp4:"2,size=8,len=dynamic"`
}

func (*Xxxx) GetType() BoxType {
	return BoxTypeXxxx()
}
```

## Install

Install go-mp4 as follows:

```sh
go get github.com/AviParampampam/go-mp4
```

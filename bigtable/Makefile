dev_build_version=$(shell git describe --tags --always --dirty)

.PHONY: ci
ci: deps checkgofmt vet staticcheck ineffassign predeclared test

.PHONY: deps
deps:
	go get -d -v -t ./...

.PHONY: updatedeps
updatedeps:
	go get -d -v -t -u -f ./...

.PHONY: install
install:
	go install -ldflags '-X "main.version=dev build $(dev_build_version)"' ./...

.PHONY: release
release:
	@go install github.com/goreleaser/goreleaser@v0.134.0
	goreleaser --rm-dist

.PHONY: docker
docker:
	@echo $(dev_build_version) > VERSION
	docker build --target=cbtemulator -t fullstorydev/cbtemulator:$(dev_build_version) ..
	@rm VERSION

.PHONY: checkgofmt
checkgofmt:
	gofmt -s -l .
	@if [ -n "$$(gofmt -s -l .)" ]; then \
		exit 1; \
	fi

.PHONY: vet
vet:
	go vet -composites=false ./...

.PHONY: staticcheck
staticcheck:
	@go install honnef.co/go/tools/cmd/staticcheck@v0.0.1-2020.1.4
	staticcheck ./...

.PHONY: ineffassign
ineffassign:
	@go install github.com/gordonklaus/ineffassign@7953dde2c7bf
	ineffassign .

.PHONY: predeclared
predeclared:
	@go install github.com/nishanths/predeclared@86fad755b4d3
	predeclared ./...

# Intentionally omitted from CI, but target here for ad-hoc reports.
.PHONY: golint
golint:
	# TODO: pin version
	@go install golang.org/x/lint/golint@latest
	golint -min_confidence 0.9 -set_exit_status ./...

# Intentionally omitted from CI, but target here for ad-hoc reports.
.PHONY: errcheck
errcheck:
	# TODO: pin version
	@go install github.com/kisielk/errcheck@latest
	errcheck ./...

.PHONY: test
test:
	go test -race ./...

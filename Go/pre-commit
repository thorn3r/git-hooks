#!/usr/bin/env bash

staged_go_files="$(git diff --staged --name-only | grep "\.go$")"

# Exit if there are no staged Go files
if [[ -z "${staged_go_files}" ]]; then
  exit 0
fi

# Set GOPATH if not already set
if [[ -z "$GOPATH" ]]; then
  GOPATH="$(go env GOPATH)"
fi

golint="${GOPATH}/bin/golint"
goimports="${GOPATH}/bin/goimports"

# Check if necessary tools are installed
if [[ ! -x "${golint}" ]]; then
  echo -e "\t\e[41mPlease install golint\e[0m (go get -u golang.org/x/lint/golint)"
  exit 1
fi

if [[ ! -x "${goimports}" ]]; then
  echo -e "\t\e[41mPlease install goimports\e[0m (go get golang.org/x/tools/cmd/goimports)"
  exit 1
fi

failed=false
warnings=false

# Perform analysis on every Go source file staged for commit
for file in ${staged_go_files}; do
  # Run goimports to add or remove imports as necessary. This may modify the file.
  "${goimports}" -w "${file}"

  # Run go fmt to check for and fix formatting and style errors. This may modify the file.
  go fmt "${file}"

  # Run go vet
  if [[ $(go vet "${file}") ]]; then
    warnings=true
    echo -e "\t\e[33mgovet ${file}\e[0m \e[1;33m\e[33mWARNING\e[0m\n"
  fi

  # Run golint
  if [[ $("${golint}" -set_exit_status "${file}") ]]; then
    failed=true
    echo -e "\t\e[31mgolint ${file}\e[0m \e[0;30m\e[41mFAILURE!\e[0m\n"
  fi
done

if [[ "${failed}" == "true" ]] ; then
  echo -e "\e[0;30m\e[41mFAILED:\e[0m \e[31mStatic analysis uncovered issues. Please revise your commit.\e[0m\n"
  exit 1
elif [[ "${warnings}" == "true" ]]; then
  echo -e "\e[0;30m\e[43mSUCCESS:\e[0m \e[33mStatic analysis complete with warnings.\e[0m\n"
else
  echo -e "\e[0;30m\e[42mSUCCESS:\e[0m \e[32mStatic analysis complete.\e[0m\n"
fi

exit 0

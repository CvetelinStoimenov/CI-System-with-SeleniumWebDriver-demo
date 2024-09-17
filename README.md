# CI Pipeline for Selenium and .NET Core

This repository contains a CI pipeline setup for a .NET Core solution using Selenium for automated testing. The pipeline is configured to run tests on three different projects within the solution. This README provides an overview of the pipeline configuration and details for each project.

## Overview

The CI pipeline is designed to:
- Build and test a .NET Core solution using GitHub Actions.
- Install necessary dependencies, including Google Chrome.
- Run tests for multiple projects using Selenium WebDriver.

## CI Pipeline Configuration

The pipeline is defined in a GitHub Actions workflow file located at `.github/workflows/ci-pipeline.yml`. It is triggered on `push` and `pull_request` events for the `main` branch.

### Pipeline Steps

1. **Checkout Code**: Pulls the repository code using `actions/checkout@v3`.
2. **Set up .NET**: Configures the .NET Core environment using `actions/setup-dotnet@v3`.
3. **Install Chrome**: Installs Google Chrome on the runner.
4. **Restore Dependencies**: Restores project dependencies using `dotnet restore`.
5. **Build Solution**: Builds the .NET Core solution.
6. **Set Execute Permissions**: Sets execute permissions for `selenium-manager` binaries.
7. **List Directory Contents**: Lists directory contents for debugging purposes.
8. **Run Tests**: Executes tests for each project.

## Projects

### 1. TestProject1

**Test Purpose**: Tests a number calculator application using Selenium WebDriver.

**Test Details**:
- **Test Class**: `TestCalculator`
- **Test Method**: `TestNumberCalculator`
- **URL**: [Number Calculator](http://softuni-qa-loadbalancer-2137572849.eu-north-1.elb.amazonaws.com/number-calculator/)

### 2. TestProject2

**Test Purpose**: Extracts product information from a web table and saves it to a CSV file.

**Test Details**:
- **Test Class**: `WorkingWithWebTable`
- **Test Method**: `TestExtractProductInformation`
- **URL**: [Product Information](http://practice.bpbonline.com/)

### 3. TestProject3

**Test Purpose**: Selects options from a dropdown menu and fetches related product information.

**Test Details**:
- **Test Class**: `WorkingWithDropDown`
- **Test Method**: `TestSelectFromDropDown`
- **URL**: [Dropdown Testing](http://practice.bpbonline.com/)

## Pipeline File

The CI pipeline configuration is located in `.github/workflows/ci-pipeline.yml`. Below is a brief overview of the configuration:

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Set up .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '6.0.x'

    - name: Install Chrome
      run: |
        sudo apt-get update
        sudo apt-get install -y google-chrome-stable

    - name: Restore dependencies
      run: dotnet restore SeleniumBasicExercise.sln

    - name: Build solution
      run: dotnet build SeleniumBasicExercise.sln --configuration Debug --no-restore

    - name: Set execute permissions for selenium-manager
      run: |
        chmod +x /home/runner/work/CI-System-with-SeleniumWebDriver-demo/CI-System-with-SeleniumWebDriver-demo/TestProject1/bin/Debug/net6.0/selenium-manager/linux/selenium-manager
        chmod +x /home/runner/work/CI-System-with-SeleniumWebDriver-demo/CI-System-with-SeleniumWebDriver-demo/TestProject2/bin/Debug/net6.0/selenium-manager/linux/selenium-manager
        chmod +x /home/runner/work/CI-System-with-SeleniumWebDriver-demo/CI-System-with-SeleniumWebDriver-demo/TestProject3/bin/Debug/net6.0/selenium-manager/linux/selenium-manager

    - name: List directory contents for debugging
      run: |
        echo "Listing contents of TestProject1/bin/Debug/net6.0/"
        ls -la /home/runner/work/CI-System-with-SeleniumWebDriver-demo/CI-System-with-SeleniumWebDriver-demo/TestProject1/bin/Debug/net6.0/
        echo "Listing contents of TestProject2/bin/Debug/net6.0/"
        ls -la /home/runner/work/CI-System-with-SeleniumWebDriver-demo/CI-System-with-SeleniumWebDriver-demo/TestProject2/bin/Debug/net6.0/
        echo "Listing contents of TestProject3/bin/Debug/net6.0/"
        ls -la /home/runner/work/CI-System-with-SeleniumWebDriver-demo/CI-System-with-SeleniumWebDriver-demo/TestProject3/bin/Debug/net6.0/

    - name: Run TestProject1 tests
      env:
        CHROMEWEBDRIVER: /usr/bin/google-chrome
      run: dotnet test TestProject1/TestProject1.csproj --verbosity normal

    - name: Run TestProject2 tests
      env:
        CHROMEWEBDRIVER: /usr/bin/google-chrome
      run: dotnet test TestProject2/TestProject2.csproj --verbosity normal

    - name: Run TestProject3 tests
      env:
        CHROMEWEBDRIVER: /usr/bin/google-chrome
      run: dotnet test TestProject3/TestProject3.csproj --verbosity normal

# testautomation_500106229
name: API Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.10"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install pipenv
          pipenv install fastapi uvicorn pytest requests

      - name: Start FastAPI server
        run: |
          pipenv run python3 apiserver.py &
          sleep 5  # Wait to ensure the server is up
        env:
          PYTHONUNBUFFERED: 1

      - name: Check if server is running
        run: |
          curl --retry 5 --retry-delay 5 --retry-connrefused http://localhost:8000 || exit 1

      - name: Run tests
        run: pipenv run pytest automation_test_pytest.py

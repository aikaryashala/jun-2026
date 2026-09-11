# Building a Algo Web Server using Python, uv, Bottle

This guide walks you through installing uv, creating a Python project in uv's project mode, and running a simple web server with Bottle.

> **Note**
>
> - **Language:** Python
> - **Package manager, project manager, virtual environment manager:** uv
> - **Web server framework:** Bottle

## What is uv?

uv is a fast tool for managing Python projects. It handles the Python version, the virtual environment, and the packages for your project, all in one tool. In **project mode**, you simply tell uv what your project needs, and it manages the virtual environment for you. You never have to create or activate it yourself.

## Step 1: Install uv

**On Omarchy**

```bash
sudo pacman -S uv
```

**On Ubuntu**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

The installer puts uv in `~/.local/bin`. Close and reopen the terminal so the new path is picked up (or run `source $HOME/.local/bin/env`). To update uv later, run `uv self update`.

**Check the installation (both systems)**

```bash
uv --version
```

If it prints a version number, you're ready.

## Step 2: Create the project

```bash
uv init algo-server
cd algo-server
```

This creates a folder with a few files, including `main.py` (a starter script) and `pyproject.toml` (the file that records your project's settings and dependencies).

## Step 3: Add Bottle

```bash
uv add bottle
```

uv creates a `.venv` folder, installs Bottle into it, and records it in `pyproject.toml` and `uv.lock`. You don't need to activate anything.

## Step 4: Write the server

Open `main.py` in any editor, delete what's there, and replace it with:

```python
from bottle import Bottle, request, run

app = Bottle()

@app.get('/')
def home():
    return "Namasthey from Algo Webserver!"

@app.get('/hello/<name>')
def hello(name):
    return {"message": f"Namasthey, {name} garu"}

@app.post('/add')
def add():
    data = request.json
    return {"sum": data["a"] + data["b"]}

if __name__ == '__main__':
    run(app, host='localhost', port=8080, debug=True, reloader=True)
```

A few things to notice in this code:

- Returning a dictionary from a route automatically sends it back as JSON.
- `<name>` in the URL is captured and passed to the function as an argument.
- `request.json` reads the JSON body of a POST request. It only works when the client sends the `Content-Type: application/json` header.
- `debug=True` shows detailed error pages, and `reloader=True` restarts the server automatically whenever you save the file.

## Step 5: Run it

```bash
uv run main.py
```

You'll see `Listening on http://localhost:8080/`. Leave this terminal running. Press `Ctrl+C` when you want to stop the server.

## Step 6: Test it

Open `http://localhost:8080/` or `http://localhost:8080/hello/Ravi` in a browser, or open a second terminal and run:

```bash
curl http://localhost:8080/
curl http://localhost:8080/hello/Ravi
curl -X POST http://localhost:8080/add \
     -H "Content-Type: application/json" \
     -d '{"a": 5, "b": 7}'
```

You should see these responses:

- `/` returns `Namasthey from Algo Webserver!`
- `/hello/Ravi` returns `{"message": "Namasthey, Ravi garu"}`
- `/add` returns `{"sum": 12}`

## Quick reference

| Task | Command |
|---|---|
| Create a project | `uv init name` |
| Add a package | `uv add package` |
| Remove a package | `uv remove package` |
| Run a file | `uv run file.py` |
| Rebuild the environment | `uv sync` |

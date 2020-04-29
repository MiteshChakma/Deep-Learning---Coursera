# Deep-Learning---Coursera


# Intro
--------

This repo contains all my work for this specialization. All the code base, quiz questions, screenshot, and images, are taken from, unless specified, Deep Learning Specialization on Coursera.



# Keeping github running for more than 30 minutes 
-------
import signal

from contextlib import contextmanager

import requests


DELAY = INTERVAL = 4 * 60  # interval time in seconds
MIN_DELAY = MIN_INTERVAL = 2 * 60
KEEPALIVE_URL = "https://nebula.udacity.com/api/v1/remote/keep-alive"
TOKEN_URL = "http://metadata.google.internal/computeMetadata/v1/instance/attributes/keep_alive_token"
TOKEN_HEADERS = {"Metadata-Flavor":"Google"}


def _request_handler(headers):
    def _handler(signum, frame):
        requests.request("POST", KEEPALIVE_URL, headers=headers)
    return _handler


@contextmanager
def active_session(delay=DELAY, interval=INTERVAL):
    """
    Example:

    from workspace_utils import active session

    with active_session():
        # do long-running work here
    """
    token = requests.request("GET", TOKEN_URL, headers=TOKEN_HEADERS).text
    headers = {'Authorization': "STAR " + token}
    delay = max(delay, MIN_DELAY)
    interval = max(interval, MIN_INTERVAL)
    original_handler = signal.getsignal(signal.SIGALRM)
    try:
        signal.signal(signal.SIGALRM, _request_handler(headers))
        signal.setitimer(signal.ITIMER_REAL, delay, interval)
        yield
    finally:
        signal.signal(signal.SIGALRM, original_handler)
        signal.setitimer(signal.ITIMER_REAL, 0)


def keep_awake(iterable, delay=DELAY, interval=INTERVAL):
    """
    Example:

    from workspace_utils import keep_awake

    for i in keep_awake(range(5)):
        # do iteration with lots of work here
    """
    with active_session(delay, interval): yield from iterable
    
    
# Keeping your connection alive during long processes
   
    ------
Workspaces automatically disconnect when the connection is inactive for about 30 minutes, which includes inactivity while deep learning models are training. You can use the workspace_utils.py module here to keep your connection alive during training. The module provides a context manager and an iterator wrapper—see example use below.

NOTE: The script sometimes raises a connection error if the request is opened too frequently; just restart the jupyter kernel & run the cells again to reset the error.

NOTE: These scripts will keep your connection alive while the training process is running, but the workspace will still disconnect 30 minutes after the last notebook cell finishes. Modify the notebook cells to save your work at the end of the last cell or else you'll lose all progress when the workspace terminates.

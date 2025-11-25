"""
api-service README

This is the main entry point for the api-service project.

Author: [Your Name]
Version: 1.0.0
"""

import os
from setuptools import setup, find_packages

def read_file(filename):
    """Read the contents of a file into a string."""
    with open(filename, 'r') as f:
        return f.read()

def get_long_description():
    """Get the long description for the package."""
    return read_file('README.md') + '\n\n' + read_file('CHANGELOG.md')

setup(
    name='api-service',
    version='1.0.0',
    description='A high-quality, scalable API service',
    long_description=get_long_description(),
    url='https://github.com/your-username/api-service',
    author='Your Name',
    author_email='your-email@example.com',
    license='MIT',
    classifiers=[
        'Development Status :: 5 - Production/Stable',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: MIT License',
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.6',
        'Programming Language :: Python :: 3.7',
        'Programming Language :: Python :: 3.8',
        'Programming Language :: Python :: 3.9',
    ],
    packages=find_packages(exclude=['tests']),
    install_requires=[
        'flask>=2.0.2',
        'flask-cors>=3.0.10',
        'flask-sqlalchemy>=2.5.1',
    ],
    extras_require={
        'dev': [
            'pytest>=6.2.2',
            'pytest-cov>=2.12.1',
            'pylint>=2.10.2',
        ],
    },
    python_requires='>=3.6',
    project_urls={
        'Bug Reports': 'https://github.com/your-username/api-service/issues',
        'Source': 'https://github.com/your-username/api-service',
    },
    keywords=[
        'api',
        'service',
        'microframework',
    ],
)
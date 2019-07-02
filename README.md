# losd
a very simple python package for SPARQL querying the LOSD (linked open social data) dataset.

### install with
    $ pip install losd

or

    $ python setup.py losd

For greater control of customization, hacking and debugging, clone the repository and install with pip using -e:

    $ git clone https://github.com/ttm/losd.git
    $ pip3 install -e <path_to_repo>

# usage

Queries have the heading:
```
PREFIX po: <http://purl.org/socialparticipation/po/>
```

Examples of usage:

```python
import losd as l
pl = l.plainQueryValues
q = l.query

# get all snapshots:
query = '''
SELECT ?s WHERE {
  ?s a po:Snapshot
}
'''

res = pl(q(query))

##########
# from here on, check to assure that the uris correspond to
# the snapshot (types) intended

# get all friendship relations in a facebook snapshot:
uri = res[99]
query = '''
SELECT ?a1 ?a2 WHERE {
?f a po:Friendship . ?f po:snapshot <%s> .
?f po:member ?a1, ?a2 .
FILTER(?a1 != ?a2)
}
''' % (uri,)
res2 = pl(q(query))


# get all retweet interactions in a Twitter snapshot:
uri = res[-1]
query = '''
SELECT ?a1 ?a2 WHERE {
?m1 po:retweetOf ?m2 . ?m1 po:author ?a1 . ?m2 po:author ?a2 .
?m1 po:snapshot <%s>
}
''' % (uri,)
res3 = pl(q(query))

# get all interactions in a email snapshot:
uri = res[48]
query = '''
SELECT ?from ?to WHERE {
?message1 po:snapshot <%s> . ?message2 po:replyTo ?message1 .
?message1 po:author ?from . ?message2 po:author ?to .
}
''' % (uri,)
res4 = pl(q(query))

# get all interactions in a IRC snapshot, with texts:
uri = res[102]
query = '''
SELECT ?a1 ?a2 ?t WHERE {
?m a po:IRCMessage . ?m po:author ?a1 . ?m po:directedTo ?a2 .
?m po:cleanText ?t . ?m po:snapshot <%s>
}
''' % (uri,)
res5 = pl(q(query))

# get all interactions in the AA snapshot:
query = '''
SELECT ?a1 ?a2 WHERE {
?s po:author ?a1 . ?s po:checkParticipant ?a2 .
}
'''
res6 = pl(q(query))


# get all friendshipts in the Participa.BR snapshot:
uri = res[104]
query = '''
SELECT ?a1 ?a2 WHERE {
?f a po:Friendship . ?f po:snapshot <%s> .
?f po:member ?a1, ?a2 .
FILTER(?a1 != ?a2)
}
''' % (uri,)
res7 = pl(q(query))

# get all interactions in the Participa.BR snapshot:
uri = res[104]
query = '''
SELECT ?a1 ?a2 WHERE {
?a po:snapshot <%s> . ?a a po:Article .
?a po:author ?a1 . ?c po:article ?a . ?c po:author ?a2 .
}
''' % (uri,)
res8 = pl(q(query))

# get all interaction in the Cidade Democrática snapshot:
uri = res[45]
query = '''
SELECT ?a1 ?a2 WHERE {
?t po:snapshot <%s> .  ?t a po:Topic . ?t po:author ?a1 .
?c a po:Comment . ?c po:topic ?t . ?c po:author ?a2 .
}
''' % (uri,)
res9 = pl(q(query))


interaction = [
    res3,
    res4,
    res5,
    res6,
    res8,
    res9
]
friendship = [
    res2,
    res7,
]

fr = []
for relations in friendship:
    fr.append(l.mkRelationNetwork(relations))

inte = []
for interactions in interaction:
    inte.append(l.mkInteractionNetwork(interactions))

# finished. Play with the networks in fr and inte
# maybe use networkx and pylab to plat them

# :::
```

### deployment to pypi
This package іs delivered by running:
  $ python3 setup.py sdist
  $ twine upload dist/

Maybe use "python setup.py sdist upload -r pypi" ?

### Further information
Further information should be found in the LOSD article repository:

### Better usage
Please consider registration into Data.World to use LOSd in accordance with their policy and facilitating assistance by their staff.
Take a look at [their own python package](https://pypi.org/project/datadotworld/).

### Contact
Any issues, questions or ideas should be sent to:

renato (dot) fabbri [AT] gmail {DOT} com

:::

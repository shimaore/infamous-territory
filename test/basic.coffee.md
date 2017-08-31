    (require 'chai').should()

    describe 'InfamousTerritory', ->
      Infamous = require '..'

      test = (a,b) ->
        c = a
        inf = new Infamous a
        inf.set b
        a.should.deep.equal b
        a.should.equal c

      it 'should insert', ->
        a = []
        b = [{_id:"1",_rev:0},{_id:"2",_rev:0}]
        test a, b

      it 'should delete', ->
        a = [{_id:"1",_rev:0},{_id:"2",_rev:0}]
        b = [{_id:"2",_rev:0}]
        test a, b

      it 'should empty', ->
        a = [{_id:"1",_rev:0},{_id:"2",_rev:0}]
        b = []
        test a, b

      it 'should replace at start', ->
        a = [{_id:"3",_rev:0},{_id:"4",_rev:0}]
        b = [{_id:"1",_rev:0}]
        test a, b

      it 'should replace at end', ->
        a = [{_id:"3",_rev:0},{_id:"4",_rev:0}]
        b = [{_id:"8",_rev:0}]
        test a, b

      it 'should replace existing', ->
        a = [{_id:"3",_rev:0},{_id:"4",_rev:0}]
        b = [{_id:"3",_rev:0},{_id:"4",_rev:1,bear:yes}]
        test a, b

      it 'should mix', ->
        a = [{_id:"3",_rev:0},{_id:"4",_rev:0}]
        b = [{_id:"2",_rev:0},{_id:"4",_rev:1,bear:yes},{_id:"5",_rev:12}]
        test a, b

      it 'should mix multiple', ->
        a = [{_id:"3",_rev:0},{_id:"4",_rev:0}]
        b = [{_id:"2",_rev:0},{_id:"25",_rev:0},{_id:"4",_rev:1,bear:yes},{_id:"42",_rev:2},{_id:"5",_rev:12}]
        test a, b

      it 'should mix many', ->
        a = [{_id:"1"},{_id:"11"},{_id:"3",_rev:0},{_id:"4",_rev:0},{_id:"43"},{_id:"56"},{_id:"67"}]
        b = [{_id:"2",_rev:0},{_id:"25",_rev:0},{_id:"4",_rev:1,bear:yes},{_id:"42",_rev:2},{_id:"5",_rev:12}]
        test a, b
